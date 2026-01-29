---
title: Trinity Large - An Open 400B Sparse MoE Model
source: Arcee AI
url: https://www.arcee.ai/blog/trinity-large
researched_at: 2026-01-29
---

# Trinity Large: An Open 400B Sparse MoE Model

## Overview

Arcee released Trinity Large, a 400B parameter sparse Mixture of Experts model with 13B active parameters per token, trained on 17 trillion tokens with sophisticated load balancing and routing techniques. The model achieves frontier performance (MMLU 87.2%) while demonstrating 2-3x faster inference throughput than weight-class competitors, with unusually high sparsity (1.56% routing fraction vs. competitors at 3-4%).

## Key Points

- **Extreme sparsity**: 256 experts with only 4 active per token yields 1.56% routing fraction—half the density of DeepSeek-V3 (3.13%)
- **Momentum-based load balancing**: Per-expert router bias adjusts based on utilization with tanh clipping and per-sequence balance loss prevents expert clustering
- **Z-loss regularization**: Lightweight mechanism stabilizing logit scale during training with continuous monitoring for early instability
- **Inference efficiency**: 2-3x faster throughput than comparable models despite increased parameter count
- **Substantial synthetic data**: 8+ trillion synthetic tokens across code, math, reasoning, and 14 non-English languages

## Technical Details

**Model Architecture:**
- 400B total parameters, 13B active per token
- 256 MoE experts, 4 experts routed per token
- 512k native context window
- Muon optimizer (enables larger critical batch sizes than AdamW)

**Training Pipeline:**
- 2048 Nvidia B300 GPUs for 33 days
- Three-phase curation: 10T base + 4T intermediate + 3T instruction tokens
- Total compute cost: ~$20M (hardware, engineering, data, storage, operations)

**Released Checkpoints:**
1. **Preview**: Chat-optimized, 128k context (8-bit)
2. **Base**: Full 17T token checkpoint
3. **TrueBase**: 10T token pre-RLHF checkpoint for studying raw pretraining

**Performance vs. Peers:**

| Benchmark | Trinity-Large | Llama 4 Maverick | Notes |
|-----------|--------------|------------------|-------|
| MMLU | 87.2% | 85.5% | Trinity leads |
| AIME 2025 (Preview) | 24.0% | 19.3% | Strong reasoning |
| Inference Speed | Baseline | 0.3-0.5x | Trinity 2-3x faster |

## Implications

**Routing design matters more than expert count**: The momentum-based load balancing with per-sequence balance loss prevents the pathological clustering that plagued earlier MoE models. This suggests practitioners focusing on open MoE architectures should prioritize routing sophistication over simply increasing expert count.

**Sparsity-performance trade-off is favorable**: At 1.56% routing fraction, Trinity achieves competitive performance while maintaining substantial inference advantages. This validates the hypothesis that aggressive sparsity with proper load balancing outperforms denser routing.

**Synthetic data at scale is table-stakes for frontier models**: The 8+ trillion synthetic tokens (47% of total training data) across specialized domains indicates that curated synthetic reasoning, code, and multilingual data is now essential for competitive performance, not optional augmentation.

**Open checkpoints have research value**: The TrueBase checkpoint at 10T tokens (pre-instruction tuning) provides rare insight into what pure pretraining produces—valuable for practitioners studying emergent capabilities vs. RLHF artifacts.

## Related

- [DeepSeek-V3 Blog](https://www.deepseek.com/) - Competing sparse MoE with 3.13% routing fraction
- [Mixture of Experts Survey](https://arxiv.org/abs/2401.04088) - Comprehensive MoE architecture overview
- [Muon Optimizer Paper](https://arxiv.org/abs/2405.11286) - Momentum optimizer enabling larger critical batches
