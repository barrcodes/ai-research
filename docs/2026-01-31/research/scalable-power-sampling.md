# Scalable Power Sampling: Training-Free Reasoning for LLMs

| | |
|---|---|
| **Source** | arXiv |
| **URL** | [arxiv.org/abs/2601.21590](https://arxiv.org/abs/2601.21590) |
| **Researched** | 2026-01-31 |

## Overview

This paper eliminates the computational bottleneck of MCMC-based power sampling by deriving a training-free algorithm that sharpens token-level distributions autoregressively. The method achieves 10x+ inference latency reduction while matching RL post-training gains on reasoning tasks without external rewards or verifiers.

## Key Points

- **Problem**: Power sampling from MCMC distributions improves LLM reasoning but is computationally prohibitive at scale due to iterative sampling costs.
- **Core Insight**: Global power distributions can be approximated by token-level scaled low-temperature distributions, where scaling captures future trajectory quality.
- **Algorithm**: Training-free, operates autoregressively on base model without fine-tuning, external rewards, or verifier components.
- **Performance**: Matches/exceeds one-shot GRPO performance across math, QA, and code tasks on four different LLMs with 10x+ latency improvement.

## Technical Details

The authors prove that sampling from the power distribution (which upweights high-quality trajectories) can be approximated through token-level temperature scaling at inference time. Rather than expensive MCMC iterations computing full trajectory rewards, the method predicts trajectory quality locally at each token step, enabling efficient autoregressive sampling.

This converts a global optimization problem (finding the best trajectory under a power distribution) into a tractable local prediction task. No RL training, verifier model, or external scoring mechanism required—just post-processing of the base model's generative distribution.

## Implications

**Adoption barrier removed**: Makes reasoning-enhanced inference practical for any organization running LLMs, since there's no training cost and latency is reasonable (10x better than MCMC, though not baseline single-pass).

**Architectural choice**: Sits between baseline greedy decoding and expensive GRPO/RL approaches. Worth evaluating when you need reasoning improvements but can't afford per-token verifiers or full retraining.

**Trade-off clarity**: 10x faster than MCMC but still meaningfully slower than single-pass. Depends on whether reasoning quality gains (matching RL-trained models) justify the latency for your deployment.

**Generalization**: Works across model scales and domains—reduces the risk of approach-specific brittleness.

## Sources

- [arXiv 2601.21590](https://arxiv.org/abs/2601.21590) - Scalable Power Sampling: Training-Free Reasoning for LLMs
