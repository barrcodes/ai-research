---
title: Open-sourcing new AI tools for adaptive experimentation
source: Meta AI Blog
url: https://ai.meta.com/blog/open-sourcing-ax-and-botorch-new-ai-tools-for-adaptive-experimentation/
researched_at: 2026-01-26T00:00:00Z
---

# Open-sourcing new AI tools for adaptive experimentation

## Overview

Meta released two complementary open-source frameworks for adaptive experimentation: **Ax**, a platform for managing and automating Bayesian optimization experiments, and **BoTorch**, a PyTorch-based library implementing modern Bayesian optimization algorithms. Together they democratize probabilistic surrogate modeling for expensive optimization problems where evaluation budgets are constrained.

## Key Points

- **Ax** provides a framework-agnostic platform with customizable optimization routines and human-in-the-loop controls, abstracting away low-level details for practitioners
- **BoTorch** implements flexible Bayesian optimization with GPU parallelism and auto-differentiation, enabling efficient batch acquisition functions via reparameterization
- **Probabilistic surrogates** outperform neural networks for small-data regimes by quantifying exploration uncertainty rather than point estimates
- **Batch optimization** via Monte Carlo acquisition functions lets practitioners evaluate multiple candidates per iteration without restrictive assumptions
- Meta deployed these for infrastructure tuning, hyperparameter optimization, feed ranking, video quality, and hardware design

## Technical Details

The architecture separates concerns cleanly:

| Component | Role | Advantage |
|-----------|------|-----------|
| **BoTorch** | Probabilistic surrogate models and acquisition functions | GPU acceleration, gradient-based optimization, flexible modeling |
| **Ax** | Experiment lifecycle management, deployment, logging | No framework lock-in, human oversight, experiment reproducibility |

**Acquisition function innovation**: BoTorch implements batch acquisition via the reparameterization trick—instead of analytic solutions, joint optimization of multiple design points uses Monte Carlo sampling. This removes restrictive conditional independence assumptions that plague alternative batch methods.

**Surrogate modeling advantage**: Gaussian processes and other probabilistic models quantify uncertainty from limited evaluations. Unlike neural networks, they don't need massive data and actively guide where to sample next—critical for expensive simulations (rocket design, clinical trials, infrastructure tuning).

## Implications

**When to use**: Problems with 100-1000 evaluations budget, unknown landscape topology, need for confidence bounds. Traditional grid search or random search wastes evaluations; gradient-free methods (Nelder-Mead) lack exploration discipline.

**Architectural trade-offs**:
- Probabilistic models scale O(n³) with data points; neural networks scale better but need more data
- Batch optimization increases wall-clock latency per iteration but improves hardware utilization
- Human-in-the-loop slows automation but prevents catastrophic failures in production (feed ranking, video QoS)

**When not to use**: High-dimensional spaces (>20-30 dims) without domain reduction, problems with under 50 evaluations, real-time control requiring sub-second decisions, or when label noise dominates.

**Competitive position**: BoTorch directly competes with Ax's older optimization engine and research systems like Spearmint. Main advantage: GPU support and PyTorch ecosystem integration reduce deployment friction for ML teams already using PyTorch.

## Related

- [BoTorch GitHub](https://github.com/pytorch/botorch) - Source code and documentation
- [Ax GitHub](https://github.com/facebook/Ax) - Experiment platform with tutorials
- Bayesian optimization surveys - Foundation theory for acquisition functions and surrogate modeling
