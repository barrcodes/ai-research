---
title: RLP - Reinforcement as Pretraining Objective
source: arXiv
url: https://arxiv.org/abs/2510.01265
researched_at: 2026-01-28
---

# RLP: Reinforcement as Pretraining Objective

## Overview

This paper inverts the conventional LLM training pipeline by integrating reinforcement learning directly into pretraining rather than relegating it to post-training. The authors propose treating intermediate reasoning steps as exploratory actions with verifier-free dense rewards computed from information gain—the increase in log-likelihood of next tokens when conditioned on reasoning traces. Results show 19-23% improvements on reasoning benchmarks without architectural changes.

## Key Points

- **Pipeline Inversion**: Moves RL from post-training phase into pretraining, developing reasoning behaviors from initialization rather than refining after convergence
- **Verifier-Free Rewards**: Eliminates the need for separate reward models by measuring how much reasoning helps predict the immediate next token, enabling dense signal across full document streams
- **Consistent Gains**: 19% improvement on 1.7B-Base (MATH, Science benchmarks), 18% on 12B model (42.81% → 61.32% on scientific reasoning); largest gains on AIME25 (+23%) and MMLU-Pro reasoning tasks
- **Ordinary Text Pipeline**: Works on standard pretraining corpora without synthetic task construction or curated reasoning datasets

## Technical Details

**Reward Mechanism**: For each token position, sample a reasoning chain (CoT) and compute:
```
R = log P(next_token | context, reasoning) - log P(next_token | context)
```

This information-gain signal captures how much the reasoning contributes to predicting the immediate future. Applies during every training step across raw pretraining data.

**Implementation**: No architectural changes needed—integrates as a loss weighting mechanism over existing next-token prediction loss. Scales to large models (tested on 1.7B and 12B parameters).

| Model | Baseline | RLP | Gain |
|-------|----------|-----|------|
| Qwen3-1.7B | ~70-80% | 89-95% | +19% avg |
| Nemotron-12B | 42.81% | 61.32% | +18.5% |

## Implications

**Architectural Significance**: This reframes reasoning capability development as a pretraining phenomenon, not a post-hoc refinement. Suggests that intermediate computation (reasoning steps) should influence gradient flow during primary training, not after.

**Trade-offs**:
- Pros: Eliminates reward model dependency, works on unstructured corpora, consistent across model scales
- Cons: Requires sampling reasoning trajectories at each step (computational overhead), effectiveness depends on how well next-token prediction captures task structure

**Actionable for Practitioners**:
- Consider integrating reasoning-focused losses earlier in training pipelines rather than defaulting to pure next-token pretraining + RL fine-tuning
- For organizations pre-training models: This suggests reasoning capability emerges more naturally when explicitly valued during pretraining
- For smaller models (1.7B): Disproportionate gains suggest this approach is particularly valuable in parameter-constrained regimes

**Future Work Implications**: Shifts focus from "how do we align models after training" to "how do we embed reasoning into the learning process itself." Opens questions about optimal reward signal design during pretraining and curriculum effects.

## Related

- [RL as a post-training phase](https://arxiv.org/abs/2309.00267) - Contrasts with conventional RLHF pipelines
- [Scaling Laws for reasoning](https://arxiv.org/abs/2305.15074) - Related work on compute allocation for CoT
- [Information gain in language modeling](https://arxiv.org/abs/2110.06088) - Theoretical foundation for reward signal design
