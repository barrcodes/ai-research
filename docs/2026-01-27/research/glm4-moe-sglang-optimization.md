# Optimizing GLM4-MoE for Production: 65% Faster TTFT with SGLang

| | |
|---|---|
| **Source** | Hugging Face Blog / Novita AI |
| **URL** | [huggingface.co/blog/novita/sglang-glm4-moe](https://huggingface.co/blog/novita/sglang-glm4-moe) |
| **Researched** | 2026-01-27 |

## Overview

Novita AI demonstrated a production-ready optimization pipeline for GLM4-MoE that achieves 65% TTFT reduction through kernel fusion, expert merging, and asynchronous data transfer. The work combines four complementary techniques—shared experts fusion, QK-norm-RoPE fusion, async transfer, and suffix decoding—validated on H200 clusters with FP8 quantization and 8-way tensor parallelism.

## Key Points

- **Shared Experts Fusion**: Merges the fixed shared expert with 160 routed experts into a unified selection pool (top 9 of 161 total), improving SM utilization and reducing memory I/O overhead by 23.7% TTFT, 20.8% ITL
- **Kernel Fusion Strategy**: QK normalization + RoPE combined into a single kernel, addressing GLM4's half-dimension rotation pattern across 92 layers
- **Async Transfer**: Eliminates TTFT bottleneck in disaggregated prefill by scheduling GPU-to-GPU transfers immediately post-kernel in background threads rather than blocking on launch completion
- **Suffix Decoding**: Model-free speculative decoding using output pattern repetition (39.3% in agentic workloads) without additional training, achieving 21.9% TPOT reduction
- **Production Status**: Fully deployed at Novita AI; majority of optimizations merged upstream in SGLang main branch

## Technical Details

**Benchmark Setup**: GLM-4.7 FP8 with TP8, 4096-token input, 1000-token output, 14 req/s request rate on H200 infrastructure.

| Optimization | TTFT Gain | TPOT Gain | Notes |
|--------------|-----------|-----------|-------|
| Shared Experts Fusion | +23.7% | +20.8% | Selects top 9 experts with fixed shared expert |
| Async Transfer | +1s under load | — | Reduces blocking in disaggregated prefill |
| Suffix Decoding | — | +21.9% | (25.13ms → 19.63ms) with NEXTN 3-step |
| **Combined Impact** | **+65%** | **+22%** | Cumulative effect on real workloads |

**Implementation**: All optimizations exposed via SGLang CLI flags (e.g., `--enable-shared-experts-fusion`, `--disaggregation-async-transfer`, `--speculative-algorithm SUFFIX`). Profiling traces and evaluation dataset (agentic_code_dataset_22 from 22 Claude Code sessions) open-sourced on GitHub.

## Implications

**When to Apply**: These techniques target MoE-specific latency bottlenecks in dense-serving scenarios (high concurrent requests, long prefills). TTFT-critical use cases (chatbots, real-time agents) benefit most; TPOT gains matter for long-context outputs.

**Trade-offs**:
- Shared experts fusion requires careful router analysis per MoE architecture; not portable across models without retuning
- Async transfer increases complexity in disaggregated setups but is transparent at the API layer
- Suffix decoding's effectiveness depends on workload characteristics; 39.3% repetition rates won't generalize to all domains

**Architectural Lessons**:
1. Kernel fusion ROI scales with layer count (GLM4's 92 layers justify QK-norm-RoPE fusion cost)
2. Disaggregated prefill introduces non-obvious scheduling bottlenecks; async patterns essential at scale
3. Speculative decoding can remain model-free if training distribution permits pattern extraction

**Integration Path**: SGLang's upstream-first approach means these become default options in next releases. Practitioners should test suffix decoding empirically per workload; shared experts fusion requires model-aware configuration.

## Related

- [SGLang GitHub: Shared Experts Fusion PR](https://github.com/sgl-project/sglang/pull/13873) - Implementation reference
- [NeurIPS 2024: SuffixDecoding Paper](https://arxiv.org/abs/2411.04975) - Theoretical foundation for pattern-based speculative decoding
- [Novita AI GitHub Branch](https://github.com/novitalabs/sglang/tree/glm_suffix) - Full code with profiling traces
- [Agentic Code Dataset](https://huggingface.co/datasets/novita/agentic_code_dataset_22) - Open evaluation data (22 sessions, 17,487 turns)
