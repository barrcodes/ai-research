# Your MoE Model Does Not Have to Select Fixed Number of Experts

| | |
|---|---|
| **Source** | Hugging Face Blog |
| **URL** | [huggingface.co/blog/Spico/dynamic-routing](https://huggingface.co/blog/Spico/dynamic-routing) |
| **Researched** | 2026-02-27 |

## Overview

Dynamic routing in Mixture of Experts models enables variable expert selection per token, abandoning the rigid fixed top-k constraint. Rather than always routing to k experts regardless of token complexity, modern approaches adaptively select the number of active experts based on input characteristics. This decoupling delivers 5-15% throughput gains while maintaining or improving model quality.

## Key Points

- **Variable Selection Replaces Fixed Top-k**: Instead of `G(x) = Top-k(softmax(xW_g))`, systems use thresholding, learned predictor functions, or probability distributions to determine per-token expert count dynamically.

- **Three Complementary Approaches**: Thresholding-based methods (cumulative probability, trainable thresholds, ReLU-based); predictor networks like Ada-K that learn optimal k per token; zero-computation experts that enable MoE++ with copy/null/constant operations at near-zero cost.

- **Zero-Computation Experts as Multiplier**: AdaMoE and MoE++ introduce expert variants that execute no computation—merely passing tokens through unchanged or applying fixed transformations. This reduces FLOPs by 15% while matching 2-3x denser models in throughput.

## Technical Details

**Thresholding Variants**

Cumulative thresholding (MoE-Dynamic) selects experts until reaching a probability threshold, improving performance 1.68% with 5% speedup. Trainable thresholding (DynMoE) uses cosine similarity against learned expert thresholds—11% throughput gains. ReMoE replaces softmax with ReLU gating:

```
G(x) = ReLU(xW_g)
```

This provides 3.67x speedup on larger models with built-in sparsity via differentiable gating and adaptive regularization coefficients.

**Learned Routing Depth (Ada-K)**

A linear predictor learns to emit k per token using PPO training. Fine-tuning only 2.95M parameters on Qwen1.5-MoE-14.3B yields 1.22x speedup. The approach separates routing frequency from routing quality—a key architectural decoupling.

**Load Balancing Evolution**

Zero-computation experts require modified load-balance losses accounting for null experts in the denominator. Real-world deployments (LongCat at 560B, Uni-MoE-2.0-Omni multi-modal) confirm viability at scale.

## Implications

**For MoE Architecture Design**

Fixed top-k is now a legacy constraint. Decision tree:
- **If targeting latency**: Thresholding methods (ReMoE) give immediate gains with minimal tuning.
- **If fine-tuning existing models**: Ada-K's parameter efficiency (2.95M) makes it retrofit-friendly.
- **If throughput-critical at scale**: Zero-computation experts compound efficiency—15% FLOPs reduction compounds with sparse activation patterns.

**Trade-Offs to Consider**

Dynamic routing trades implementation complexity for efficiency. Grouped GEMM kernels break under variable sparsity; specialized sparse kernels become mandatory. Load-balancing losses must account for null experts. Sparsity regularization requires careful tuning—too aggressive causes activation collapse.

**When to Stay Fixed**

Fixed top-k remains sensible for systems where:
- Latency predictability matters more than throughput (static expert selection is more cache-friendly)
- Kernel libraries are immature for sparse operations
- Token complexity distributions are genuinely uniform

Modern benchmarks suggest dynamic routing should be the baseline assumption for new MoE work, with fixed-k reserved for latency-critical deployments.

## Sources

- [Hugging Face Blog: Dynamic Routing in MoE](https://huggingface.co/blog/Spico/dynamic-routing) - Comprehensive comparison of thresholding, predictor, and zero-computation approaches with implementation details and benchmarks
