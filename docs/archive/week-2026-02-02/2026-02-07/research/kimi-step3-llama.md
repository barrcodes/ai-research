# Kimi-Linear and Step3.5-Flash Ready for llama.cpp

| | |
|---|---|
| **Source** | GitHub (stepfun-ai, ggml-org, discussion threads) |
| **URL** | [github.com/ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp) |
| **Researched** | 2026-02-07 |

## Overview

Two major model architectures have achieved production-ready llama.cpp support: Step3.5-Flash, a sparse Mixture-of-Experts (MoE) model that runs 11B speed with 196B parameter coverage, and Kimi-Linear, featuring Multi-head Latent Attention (MLA) for optimized KV cache efficiency. Both implementations demonstrate how architectural innovations in attention mechanisms and expert routing directly reduce the memory and computational footprint required for local deployment.

## Key Points

- **Step3.5-Flash architecture**: Sparse MoE with 288 routed experts per layer (Top-8 selection) plus 1 shared expert—achieves near-10x model compression while maintaining inference quality
- **Kimi-Linear MLA**: Multi-head Latent Attention reduces KV cache size through latent-space projection, implemented as backend-agnostic support with CPU/CUDA parity
- **Long-context scaling**: Step3.5-Flash degrades only ~58% on prefill and ~43% on generation across 100k context window (vs. steeper drops in competing models)
- **Hardware-agnostic deployment**: Both models validated on Mac Studio M4 Max, NVIDIA DGX-Spark, and AMD Ryzen AI Max+ 395 with minimal performance variance

## Technical Details

**Step3.5-Flash Performance Benchmarks**:

| Platform | Prompt (16K tokens) | Generation | Extended Context (262K) |
|---|---|---|---|
| Mac M4 Max | 372.5 tok/s | 41.3 tok/s | 119.5 prefill / 8.88 gen |
| DGX-Spark | 528.4 tok/s | 19.2 tok/s | 217.2 prefill / gen |
| AMD Ryzen AI Max+ 395 | 43.1 tok/s | 2.98 tok/s | — |

**Kimi-Linear Implementation**:
- Backend-agnostic design with separate `ggml-cpu` and `ggml-cuda` paths
- MLA treated as hybrid architecture for proper KV cache management
- Replaced custom recurrent operations with efficient autoregressive approach
- Tokenizer: DeepSeek V2 compatible

**KV Cache Optimization**: Step3.5-Flash supports KV cache quantization (e.g., 103.84 GiB at Q4_K) enabling deployment on consumer hardware.

## Implications

**For local deployment architects**:
- Sparse MoE reduces memory budget by 10-20x vs. dense models while preserving capability—critical for edge/embedded inference
- MLA's latent-space KV projection offers a path forward from dense attention bottlenecks without requiring position-interpolation hacks
- Long-context degradation curves suggest viability for agentic workflows (CLI agents, RAG systems) without dynamic batching complexity

**Trade-offs to consider**:
- Top-K expert selection adds routing overhead; prefill throughput peaks at lower batch sizes than dense models
- MLA requires backend-specific implementations (though now standardized in llama.cpp)
- Generation speed (8-41 tok/s) remains compute-bound; gains most valuable for prefill-heavy workloads (prompt caching scenarios)

**When to adopt**:
- Step3.5-Flash: 128GB+ unified memory systems requiring sub-10B effective inference speed; agentic coding workflows with cached prompts
- Kimi-Linear: When KV cache memory is the bottleneck; models designed for MLA natively (Kimi K2/K2.5)

**Architectural significance**: Both represent convergence around *selective computation* (expert routing, latent attention) as the dominant scaling strategy for local models—moving away from brute-force quantization toward algorithmic efficiency.

## Sources

- [stepfun-ai/Step-3.5-Flash](https://github.com/stepfun-ai/Step-3.5-Flash) - Sparse MoE model with llama.cpp support and performance benchmarks
- [ggml-org/llama.cpp PR #18755](https://github.com/ggml-org/llama.cpp/pull/18755) - Kimi-Linear MLA KV cache implementation
- [ggml-org/llama.cpp Discussion #19272](https://github.com/ggml-org/llama.cpp/discussions/19272) - Step 3.5 Flash int4 benchmarks and long-context performance analysis
