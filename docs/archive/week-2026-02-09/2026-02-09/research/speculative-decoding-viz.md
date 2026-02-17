# Speculative Decoding Visualization - Token Generation Insights

| | |
|---|---|
| **Source** | NVIDIA Technical Blog, Google Research, BentoML, vLLM Documentation |
| **URL** | [developer.nvidia.com/blog/tensorrt-llm-speculative-decoding](https://developer.nvidia.com/blog/tensorrt-llm-speculative-decoding-boosts-inference-throughput-by-up-to-3-6x/) |
| **Researched** | 2026-02-09 |

## Overview

Speculative decoding pairs a lightweight draft model with a target model to parallelize token generation. The draft model proposes K candidate tokens per iteration; the target model verifies all candidates in a single forward pass through rejection sampling. This draft-then-verify loop guarantees output distributions match the target model exactly while collapsing sequential generation steps into batched operations, achieving 2.2x–3.6x throughput improvements without quality degradation.

## Key Points

- **Draft-Then-Verify Pattern**: Draft model generates 3–12 candidate tokens; target model computes probability distributions across all positions simultaneously and accepts tokens where target probability ≥ draft probability. Output is mathematically identical to running the target model alone.

- **Performance Gains**: TensorRT-LLM benchmarks show 3.33x–3.61x speedup on Llama 3.1 405B with 4 H200 GPUs (111–121 tokens/sec vs. 33 baseline). Llama 3.1 70B achieves 2.23x–2.86x on single H200. Real-world improvements hit 2–3x across translation, summarization, and search applications.

- **Acceptance Rate Critical**: Token acceptance rate (α) directly determines speedup potential. When draft predictions align with target model outputs, parallelization is maximized; if rejected, only one token is generated (zero benefit). Draft model quality, domain tuning, and target model complexity all affect this rate.

- **Implementation Variants**: Traditional approach uses separate trained draft model (requires dual model management); EAGLE-3 attaches lightweight autoregressive prediction head to target model's internal layers, eliminating separate draft model while improving acceptance rates through dynamic candidate trees. EAGLE-3 requires model conversion via TensorRT-Model Optimizer.

- **Memory & Compute Trade-offs**: Requires loading both models in VRAM. Single-GPU deployments face constraints; multi-GPU setups with tensor parallelism see better utilization. FP8 quantization, fused operations, and paged KV cache management mitigate overhead.

## Technical Details

### Benchmarks (TensorRT-LLM)

| Setup | Draft Model | Tokens/Sec | Speedup |
|-------|-------------|-----------|---------|
| Llama 3.1 405B (4×H200) | Llama 3.2 1B | 111.34 | 3.33x |
| Llama 3.1 405B (4×H200) | Llama 3.2 3B | 120.75 | 3.61x |
| Llama 3.1 70B (1×H200) | Llama 3.2 1B | 146.05 | 2.86x |
| Llama 3.1 70B (1×H200) | Llama 3.2 3B | 140.49 | 2.75x |

### Configuration Example

```
Build flag: --speculative_decoding_mode=draft_tokens_external --max_draft_len 10
Runtime config: [10,[0,1,2,3,4,5,6,7],[0,1,2,3,4,5,6,7],False]
  - First value: draft length (K tokens)
  - Subsequent: GPU device assignments
  - Enables paged context FMHA: --use_paged_context_fmha=enable
```

### Latency Example

Standard generation of 3 tokens: 600ms (200ms per token × 3 sequential passes).
Speculative decoding of 3 tokens: ~250ms (draft proposal + parallel target verification).
**Result: 2.4x latency reduction for short sequences.**

## Implications

**Use speculative decoding when:**
- Latency-sensitive applications matter (chatbots, code completion, AI Overviews)
- Multi-GPU setups with tensor parallelism enable parallel verification without memory bottlenecks
- Draft-target alignment targets ≥60% acceptance rate through domain-specific tuning or proper model sizing
- Hardware supports parallel batch operations (modern H100/H200, A100 with sufficient memory)

**Skip speculative decoding when:**
- Memory constraints prohibit dual-model loading (most single-GPU consumer hardware)
- Draft model accuracy is unpredictable (mismatched tokenizer, domain shift, untrained drafts)
- Throughput is the goal but latency-per-token isn't critical (batch inference with long sequences where parallelization gains diminish)
- Output quality uncertainty—must verify output distributions remain identical

**Competing Techniques:**
PagedAttention and prefill-decode disaggregation optimize KV cache and computation scheduling but don't parallelize sequential generation. Continuous batching improves throughput for request queues. Quantization (FP8) and flash attention reduce per-token latency independently. EAGLE-3 is the most recent advancement, reducing memory footprint by eliminating separate draft model while improving acceptance rates through multi-layer feature extraction.

**Implementation Considerations:**
- Fine-tune domain-specific draft models for production use (generic drafts underperform)
- Monitor acceptance rates; falling below 50% signals draft-target misalignment
- TensorRT-LLM, SGLANG, and vLLM all support speculative decoding; choose framework based on existing inference pipeline
- Batch sizes up to 32 and sequence lengths to 131k are supported in optimized implementations

## Sources

- [NVIDIA: Introduction to Speculative Decoding](https://developer.nvidia.com/blog/an-introduction-to-speculative-decoding-for-reducing-latency-in-ai-inference/) - Core mechanism and latency improvements
- [NVIDIA: TensorRT-LLM Speculative Decoding 3.6x Throughput](https://developer.nvidia.com/blog/tensorrt-llm-speculative-decoding-boosts-inference-throughput-by-up-to-3-6x/) - Benchmarks and implementation details
- [BentoML: LLM Inference Handbook - Speculative Decoding](https://bentoml.com/llm/inference-optimization/speculative-decoding) - Practical considerations and use cases
- [Google Research: Looking Back at Speculative Decoding](https://research.google/blog/looking-back-at-speculative-decoding/) - Real-world applications and effectiveness
- [vLLM: Speculative Decoding Documentation](https://docs.vllm.ai/en/latest/features/spec_decode/) - Framework integration reference
