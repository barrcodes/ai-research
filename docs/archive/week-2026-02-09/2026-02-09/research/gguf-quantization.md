# GGUF Quantization Benchmarks - Quality vs. Speed

| | |
|---|---|
| **Source** | Web research: GGUF quantization benchmarks 2025-2026 |
| **URL** | [Research compilation](https://local-ai-zone.github.io/guides/what-is-ai-quantization-q4-k-m-q8-gguf-guide-2025.html) |
| **Researched** | 2026-02-09 |

## Overview

GGUF quantization enables efficient local LLM deployment by reducing model precision from FP16 to 2-8 bit formats, achieving 4-8x memory compression while maintaining 92-95% output quality. Modern K-quants (hierarchical quantization) dominate production use, while I-quants offer aggressive compression at the cost of slower token generation. For practitioners, the choice hinges on hardware constraints and tolerance for accuracy loss.

## Key Points

- **Q4_K_M dominates**: 50% smaller than FP16, 95% quality retention, 3-4x faster inference. Reduces Llama 2 13B from 26GB to 7.9GB—runnable on 12GB RAM vs. 32GB+ for FP16.

- **Quantization hierarchy matters**: K-quants (Q4_K_M, Q5_K_M, Q6_K) use block + super-block hierarchical scaling for quality. I-quants (IQ4_XS, IQ3_XS, IQ2_XXS) maximize compression via lookup tables but sacrifice 20-40% throughput due to decode complexity.

- **Speed-quality trade-off is non-linear**: Lower bits don't always mean faster inference. I-quants compress more aggressively but involve indexing overhead that reduces tokens/second. Q4_K_M achieves practical optimal speed (22-30 tokens/sec on consumer CPUs) with acceptable accuracy.

- **Bit-width sweet spot depends on hardware**: 8-bit (Q8_0) is production-ready with minimal loss. 4-bit (Q4_K_M/Q4_K_S) balances compression and speed. 2-3 bit (IQ quants) suits ultra-low-resource scenarios where speed loss is acceptable.

## Technical Details

| Quantization | Bit Width | Model Reduction | Quality Loss | Tokens/Sec | Best For |
|---|---|---|---|---|---|
| FP16 (baseline) | 16 | — | 0% | 8 | High-end systems, baselines |
| Q8_0 | 8 | 2x | <1% | 12 | Accuracy-critical deployments |
| Q5_K_M | 5 | 3.2x | ~1-2% | 14 | Balance-first deployments |
| Q4_K_M | 4 | 3.25x | 5-8% | 22-30 | Most consumer deployments |
| Q4_K_S | 4 | 3.0x | 5-8% | 20 | VRAM-constrained (8GB) |
| IQ4_XS | 4 | 4.0x | 3-5% | 12-16 | Calibration-aware, lower speed |
| IQ2_XXS | 2 | 8x | 15-20% | 6-10 | Extreme compression only |

**Benchmark data** (Llama 2 13B on CPU, M3 Apple Silicon):
- Q4_K_M: 7.9GB, 95% quality, 22 tokens/sec
- Q5_K_M: 9.1GB, 99% quality, 14 tokens/sec
- FP16: 26GB, 100% quality, 8 tokens/sec

**KL-divergence quality metrics** (lower = better):
- Q6_K: 0.05 median divergence (near-lossless)
- Q5_K_M: 0.08 median divergence (imperceptible degradation)
- Q4_K_M: 0.12-0.15 median divergence (acceptable for most tasks)
- IQ2_XXS: 0.40+ (noticeable but usable for cheap inference)

## Implications

**When quantization is worth the trade-off:**

1. **CPU-only deployment**: GGUF Q4_K_M is mandatory. 4-8GB device constraints make anything larger impossible without quantization. Accept 5-8% quality loss in exchange for viability.

2. **Latency-sensitive applications**: Q4_K_M achieves 2.75x speedup over FP16 (22 vs 8 tokens/sec). Production chat systems benefit from sub-100ms per-token performance on consumer hardware.

3. **Cost-constrained inference**: Running 13B models on 12GB systems instead of renting GPU time saves 70-90% operational costs. Quality loss (5-8%) acceptable for summarization, classification, retrieval tasks.

4. **When quality loss is unacceptable**: Use Q6_K (near-lossless, 1.5x compression) or Q5_K_M (99% quality, 3.2x compression) for code generation, medical analysis, high-stakes decision support. Benchmark against your task's baseline first.

**Key decision matrix:**

- **8GB laptop, 5% quality OK**: Q4_K_M (llama.cpp or Ollama)
- **16GB laptop, precision matters**: Q5_K_M or Q6_K
- **GPU available (RTX 3060+)**: GPTQ or AWQ (better throughput than GGUF on NVIDIA)
- **Ultra-low resource (2-4GB)**: IQ quants despite speed penalty
- **Critical accuracy (coding, medical)**: Benchmark Q5_K_M first, avoid IQ quants

**Practical workflow:**

1. Profile baseline task performance on full FP16 model
2. Test Q4_K_M as starting point (highest ROI)
3. Run 10-prompt evaluation against your task (don't trust generic benchmarks)
4. Step up to Q5_K_M only if quality drop exceeds tolerance
5. Use llama.cpp's `-t` parameter tuning for CPU threading optimization
6. Monitor context window (n_ctx) settings—VRAM scales linearly with window size

**Tools landscape:**

- **llama.cpp**: Fastest CPU inference, supports all GGUF variants, active development
- **Ollama**: User-friendly wrapper over llama.cpp, cross-platform
- **LM Studio**: GUI-based, good for experimentation
- **vLLM**: GPU-optimized, supports GPTQ/AWQ via CUDA

K-quants have essentially replaced legacy quantization (Q4_0, Q5_0). Avoid I-quants unless you're optimizing for extreme compression with non-interactive workloads (batch processing, overnight jobs).

## Sources

- [AI Model Quantization 2025: Master Compression Techniques](https://local-ai-zone.github.io/guides/what-is-ai-quantization-q4-k-m-q8-gguf-guide-2025.html) - Performance benchmarks and quality trade-offs
- [Choosing a GGUF Model: K-Quants and I-Quants](https://kaitchup.substack.com/p/choosing-a-gguf-model-k-quants-i) - Technical deep-dive on quantization types
- [GGUF Quantization Overview](https://gist.github.com/Artefact2/b5f810600771265fc1e39442288e8ec9) - KL-divergence benchmarks and speed metrics
- [GGUF vs GPTQ vs AWQ (2026)](https://localaimaster.com/blog/quantization-explained) - Format comparison and hardware recommendations
- [LLMs on CPU: The Power of Quantization](https://www.ionio.ai/blog/llms-on-cpu-the-power-of-quantization-with-gguf-awq-gptq) - CPU inference strategies and practical deployment
- [Which Quantization Method Is Best for You?](https://www.e2enetworks.com/blog/which-quantization-method-is-best-for-you-gguf-gptq-or-awq) - Comparative analysis across formats
