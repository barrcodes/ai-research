# Smallest Transformer That Can Add Two 10-Digit Numbers

| | |
|---|---|
| **Source** | GitHub: AdderBoard Project + Hacker News Discussion |
| **URL** | [github.com/anadim/AdderBoard](https://github.com/anadim/AdderBoard) |
| **Researched** | 2026-02-27 |

## Overview

AdderBoard is a competitive challenge exploring the minimal transformer architecture needed to add two 10-digit numbers. The project reveals a fundamental gap between hand-engineered and learned solutions: hand-coded weights achieve 36 parameters (100% accuracy), while gradient-descent trained models converge at 311 parameters (99.999% accuracy). This 9× gap exposes how SGD and human algorithm design explore fundamentally different parameter spaces.

## Key Points

- **Hand-coded best:** 36 parameters using ALiBi slope=log(10) base-10 weighting, sparse embeddings, gated ReLU FFN, and float64 precision
- **Trained best:** 311 parameters using rank-3 factorization, single decoder layer, d=7, 1 attention head, ff=14
- **Architecture convergence:** Successful trained models consistently reduce to single-layer designs, contradicting intuition about depth
- **Parameter cliff:** Sharp accuracy transitions occur around 800 parameters—below this threshold, addition fails catastrophically
- **Factorization critical:** Rank-3 weight factorization became essential for compressing trained models below practical thresholds

## Technical Details

The constraint requires genuine autoregressive transformers with self-attention, forcing models to learn digit alignment, per-digit arithmetic, and carry propagation through weights rather than procedural code.

Early trained models discovered this architecture naturally:
- Single decoder layer (2+ layers underperform at equivalent budgets)
- d=7 embedding dimension
- 1 attention head
- ff=14 feedforward dimension
- Tied embeddings

The hand-coded 36-parameter solution directly implements carry-propagation logic using ALiBi positional biasing and gated ReLU activations, but critically depends on float64 numerical precision—a constraint incompatible with gradient descent optimization, which typically operates in float32.

## Implications

**For mechanistic interpretability:** The 9× gap between engineered and learned solutions indicates neural networks discover messier, more distributed representations than human-designed algorithms. This "sloppiness" may provide unexpected benefits—learned solutions potentially generalize better across variations despite reduced interpretability.

**For arithmetic in transformers:** Single-layer transformers suffice for 10-digit addition when properly parameterized, challenging assumptions about depth requirements. However, the parameter cliff suggests arithmetic may require stepping past a critical phase transition in capacity.

**For architecture search:** Hand-coding optimal solutions is ineffective for finding learnable architectures. Gradient descent explores a distinct solution space where over-parameterization aids discovery, then post-training compression becomes necessary—suggesting architecture search benefits more from learning than engineering.

**Practical consideration:** The 36-parameter hand-coded solution's float64 dependency highlights a real constraint: minimal solutions designed for inference may not be discoverable through standard training pipelines.

## Sources

- [GitHub: anadim/AdderBoard](https://github.com/anadim/AdderBoard) - Central project repository with submissions and leaderboard
- [Hacker News Discussion](https://news.ycombinator.com/item?id=47170030) - Community discussion on learned vs. hand-coded solutions and implications
- [28-parameter transformer gist](https://gist.github.com/SeuperHakkerJa/da3050739bea97aabd86ee0d7d5ef689) - Minimal working example
