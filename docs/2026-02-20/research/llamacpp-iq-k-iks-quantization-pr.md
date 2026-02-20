# llama.cpp PR - Implement IQ*_K and IQ*_KS Quantization Formats

| | |
|---|---|
| **Source** | GitHub - ggml-org/llama.cpp |
| **URL** | [github.com/ggml-org/llama.cpp/pull/19726](https://github.com/ggml-org/llama.cpp/pull/19726) |
| **Researched** | 2026-02-20 |

## Overview

This PR ports five new quantization formats (IQ2_K through IQ6_K) from the ik_llama.cpp project into mainline llama.cpp, enabling efficient local inference with minimal quality degradation. The formats provide a spectrum of compression-to-accuracy trade-offs, with IQ2_K achieving ~2.73 bits-per-weight and IQ6_K at ~6.63 BPW. The implementation currently targets CPU backends, with GPU support planned for future iterations.

## Key Points

- **Five quantization formats** spanning 2.73 to 6.63 bits-per-weight, directly porting existing ik_llama.cpp implementations
- **Minimal quality loss**: Perplexity ratios range 0.996-1.009 across formats; KL divergence stays 0.005-0.012, indicating strong inference consistency
- **Practical file sizes**: Models range 1.28 GB (IQ2_K, 4B model) to 3.1 GB (IQ6_K), enabling deployment on resource-constrained hardware
- **Interoperability**: Resolves format mismatch between ik_llama.cpp and mainline llama.cpp, allowing seamless model loading and export
- **IQ*_KS deferred**: Author simplified review scope by excluding IQ*_KS variants (which involve additional row metadata) to future PRs

## Technical Details

The implementation directly ports code from ik_llama.cpp with attribution rather than complete rewrite. Modifications target quantization subsystem files (`ggml/src/ggml-quants.c` and related handlers). Testing on Qwen3-4B-Instruct demonstrates consistent behavior:

| Format | BPW | PPL Ratio | Mean KLD | Model Size |
|--------|-----|-----------|----------|------------|
| IQ2_K | 2.73 | 1.009 | 0.012 | 1.28 GB |
| IQ3_K | 3.57 | 1.005 | 0.008 | ~1.64 GB |
| IQ4_K | 4.62-4.72 | 0.998 | 0.007 | ~2.14 GB |
| IQ5_K | 5.60 | 0.997 | 0.006 | ~2.57 GB |
| IQ6_K | 6.63 | 0.996 | 0.005 | 3.1 GB |

Top-P sampling alignment reaches 93-96% across formats, indicating the quantized models preserve decoding behavior.

## Implications

**For local inference practitioners**: This PR significantly expands quantization options beyond existing Q*_K formats, enabling finer-grained optimization of model size versus quality. At 2.73 BPW, IQ2_K pushes aggressive compression while maintaining usable output—suitable for edge deployment. At 6.63 BPW, IQ6_K approaches original model fidelity while still providing 2-3x compression.

**Architectural decision**: CPU-first implementation suggests the formats prioritize compatibility and validation before GPU optimization. Expect CUDA/Vulkan variants in follow-up PRs once this proves stable.

**Integration concern**: Loading ik_llama.cpp models in mainline llama.cpp previously required external conversion; this PR eliminates that friction point, reducing operational overhead for teams managing both codebases.

## Sources

- [ggml-org/llama.cpp/pull/19726](https://github.com/ggml-org/llama.cpp/pull/19726) - PR implementing IQ*_K quantization formats from ik_llama.cpp
