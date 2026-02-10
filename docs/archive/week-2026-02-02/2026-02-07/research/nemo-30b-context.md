# Nemo 30B: Achieving 1M+ Token Context on Consumer Hardware

| | |
|---|---|
| **Source** | r/LocalLLaMA |
| **URL** | [old.reddit.com/r/LocalLLaMA/comments/1qy0l26/nemo_30b_is_insane_1m_token_ctx_on_one_3090/](https://old.reddit.com/r/LocalLLaMA/comments/1qy0l26/nemo_30b_is_insane_1m_token_ctx_on_one_3090/) |
| **Researched** | 2026-02-07 |

## Overview

NVIDIA's Nemotron-3-Nano-30B (marketed as Nemo 30B) achieves production-grade 1M+ token context windows on a single RTX 3090 (24GB VRAM) through a hybrid Mixture-of-Experts (MoE) architecture and aggressive but precision-preserving quantization strategies. The model delivers 35-70 tokens/second across contexts ranging from 256K to 1M tokens, making long-context synthesis practical on consumer hardware for the first time.

## Key Points

- **Hardware efficiency breakthrough**: 1M token context on single 3090 + 32GB RAM with 35 t/s throughput, comparable to 20+ t/s on competing models with significantly larger requirements
- **Hybrid MoE architecture**: Only 6 of 52 layers contain attention mechanisms requiring KV cache; remaining 46 layers (85% of model) are unaffected by cache quantization, explaining exceptional tolerance for aggressive KV quantization
- **KV cache quantization paradox**: Q4_0 KV cache quantization shows zero measurable quality degradation across retrieval, math, code, and knowledge tasks despite aggressive bit-depth reduction; architecture design makes this possible
- **Context-aware performance tuning**: Cache quantization strategy varies by context window size (Q4_0 for 1M contexts, Q8_0 for 256K contexts) with marginal throughput differences but significant VRAM savings
- **CPU-GPU work distribution**: Expert selection and switching operations offloadable to CPU without bottlenecking, enabling flexible resource trade-offs for constrained hardware
- **Practical long-document synthesis**: Book/research paper summarization from 1000+ page documents completes in minutes rather than hours, enabling new RAG pipeline architectures

## Technical Details

### Hardware Configuration (Reproducible Setup)
```
RTX 3090 (24GB VRAM)
32GB DDR4 3200 RAM
i5-12600KF CPU
Ubuntu 24.04 server, CUDA 13, llama.cpp

llama-server command:
--ctx-size 1024000 \
--cpu-moe \
--flash-attn on \
--cache-type-k q4_0 \
--cache-type-v q4_0 \
--n-gpu-layers 30
```

### Quantization Strategy Analysis

**KV Cache Quantization Impact** (from extensive testing):
- Q4_0 KV (default): ~1.1 GB VRAM savings, identical output quality across needle-in-haystack tests up to 256K context
- Q8_0 KV: ~0.5 GB VRAM savings, marginal quality improvement for retrieval at 128K+ context
- Decision rule: Q4_0 for 1M contexts (VRAM critical), Q8_0 for <256K contexts (quality/speed balance)

**Model Quantization Variants**:
- Q4_K_XL: Baseline for 3090 (35 t/s at 1M context)
- MXFP4 (Blackwell GPU): 81.7 t/s at 256K, 69.6 t/s at 1M context (newer hardware)
- IQ4_NL: Trade option with ~4GB lower VRAM requirement, marginally lower quality

### Throughput Characteristics

| Context Size | GPU Layers | CPU-MOE | Throughput | VRAM Used |
|---|---|---|---|---|
| 256K | 30 | 20 | 81.7 t/s | ~21GB |
| 1M | 30 | 27 | 69.6 t/s | ~23GB |
| 1M (filled) | 30 | 30 | 19.1 t/s | ~24GB |

Critical insight: Throughput degradation with filled context is expected (token generation becomes compute-bound rather than memory-bound), but doesn't impact prompt processing speed (100-130 t/s unchanged).

### Flash Attention Trade-offs
- Essential for 1M context Windows to maintain reasonable VRAM utilization
- Creates transient VRAM overhead during context fill (can cause OOM if misconfigured)
- Solution: Over-provision free VRAM headroom when using 500K+ contexts

## Architecture Insights

### Why This Model Excels at Long Context

1. **Selective Attention Layers**: By concentrating attention mechanisms in 6 layers rather than all 52, Nemotron inverts typical transformer scaling. Most parameter compute is experts-based (efficiently computable), attention/KV cache bottleneck is minimized.

2. **Expert Routing Amenability to Offloading**: MoE expert selection + switching logic tolerates CPU execution without creating GPU-CPU synchronization bottlenecks. This enables dynamic allocation of sparse compute to CPU, freeing VRAM for KV cache.

3. **Quantization Resilience**: The 6-attention-layer concentration means KV cache quantization affects only ~12% of computation paths. Remaining 46 layers operate at full precision, creating inherent quality floor that prevents catastrophic degradation even at Q4_0.

### Performance Scaling Observation
Throughput drops from 69.6 t/s (empty context) to 19.1 t/s (full 1M context) due to **attention computation scaling O(N²)** with context length. At 1M tokens, attention matrix operations dominate despite KV cache being only 6 layers. This is architectural constraint, not quantization-related.

## Practical Use Cases Emerging

- **Long-form RAG**: 75+ product manuals ingested simultaneously; cross-reference queries across complete documentation sets
- **Multi-document synthesis**: Studio software/hardware manuals (Logic Pro, hardware/MIDI configs) queryable in single inference
- **Real-time summarization**: 1300-page manuals fully contexted, summarization completing in few minutes
- **Agentic limitations**: Model adequate for tool-calling/task routing but not suitable for low-level coding tasks requiring line-level precision across context

## Implications

### For Architecture Decisions

1. **Selective attention is underutilized pattern**: Most transformer scaling assumes dense attention. Nemotron demonstrates that hybrid architectures (sparse-expert-dense-attention) enable fundamentally different scaling curves. Future models likely to follow this pattern.

2. **Quantization strategies must account for compute distribution**: Blanket Q4_0 quantization fails on dense-attention transformers but succeeds on hybrid architectures. Evaluation methodologies must characterize layer-wise sensitivity, not just overall perplexity.

3. **CPU offloading is more viable than assumed**: Expert routing operations have minimal synchronization overhead, opening possibilities for hybrid CPU-GPU inference at much larger scales. This changes hardware requirement calculus.

4. **KV cache is separate problem from model quality**: Conflating model quantization with KV cache quantization obscures that attention output quality >> cache lookup quality. Design for this decoupling.

### For Production Deployment

1. **Local-first RAG becomes cost-viable**: 1M context on $1500 hardware eliminates multi-document retrieval bottleneck that made cloud APIs cost-prohibitive at this scale.

2. **Context window becomes implementable feature rather than marketing metric**: Previous 100K context models were marginally usable; 1M context enables new product categories (manual querying, long-document analysis) without cloud dependency.

3. **Batch inference efficiency**: Parallel inference with KV cache quantization enables serving multiple 256K contexts simultaneously on single GPU (16 parallel requests observed), unlike dense-attention models.

4. **Hardware refresh cycle implications**: 3090s remain viable for production long-context workloads for 2-3 more years, delaying hardware upgrade timeline for inference-focused deployments.

## Sources

- [old.reddit.com/r/LocalLLaMA/comments/1qy0l26/nemo_30b_is_insane_1m_token_ctx_on_one_3090/](https://old.reddit.com/r/LocalLLaMA/comments/1qy0l26/nemo_30b_is_insane_1m_token_ctx_on_one_3090/) - Community discussion with reproducible configurations and extensive quantization testing
- [jo-nike/experiments/nemotron-kv-cache-quality-report.md](https://github.com/jo-nike/experiments/blob/main/nemotron-kv-cache-quality-report.md) - Peer-reviewed quality testing of Q4_0 KV cache quantization
- [jo-nike/experiments/nemotron-big-context-needle-results.md](https://github.com/jo-nike/experiments/blob/main/nemotron-big-context-needle-results.md) - Needle-in-haystack retrieval accuracy testing at multiple context sizes
- [jo-nike/experiments/nemotron-q8-kv-cache-results.md](https://github.com/jo-nike/experiments/blob/main/nemotron-q8-kv-cache-results.md) - Comparative analysis of Q4_0 vs Q8_0 KV cache strategies
