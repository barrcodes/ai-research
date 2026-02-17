# On Randomness in Agentic Evals

| | |
|---|---|
| **Source** | arXiv Research Paper |
| **URL** | [arxiv.org/html/2602.07150](https://arxiv.org/html/2602.07150) |
| **Researched** | 2026-02-11 |

## Overview

A large-scale empirical study from KTH Royal Institute of Technology quantifies substantial variance in agentic system evaluations. By running 60,000 independent agent trajectories on SWE-Bench-Verified (spanning 3 models and 2 scaffolds), researchers demonstrate that single-run pass@1 estimates vary by 2.2–6.0 percentage points, with standard deviations exceeding 1.5 points even at temperature 0. This fundamentally challenges the current practice of reporting single-run evaluation scores as reliable performance estimates.

## Key Points

- **Variance exceeds claimed improvements**: Single-run pass@1 estimates can vary by 2.2–6.0 percentage points depending on which run is selected. Many published improvements claiming 2–3 percentage points likely fall within evaluation noise rather than representing genuine algorithmic progress.

- **Determinism is illusory**: Even at temperature 0 (theoretically deterministic), standard deviations exceed 1.5 percentage points. Non-determinism arises from inference engines, environment execution, and timing effects—not just sampling.

- **Early trajectory divergence cascades**: Trajectories diverge early, often within the first few percent of tokens. These initial stochastic differences cascade into fundamentally different solution strategies through autoregressive conditioning, making later determinism irrelevant.

- **Massive performance envelope gaps**: Using pass@k (optimistic: at least one of k attempts succeeds) and pass^k (pessimistic: all k attempts succeed), researchers found gaps up to 24.9 percentage points between best-case and worst-case performance—revealing how much success depends on stochastic exploration rather than deterministic capability.

- **Scale of the study**: 60,000 agent trajectories generating 25.58B tokens and 1.88M tool calls across Qwen3-32B, Claude-3.5-Sonnet, and o1-mini with varying scaffolds and temperatures.

## Technical Details

**Experimental setup**: Six agent configurations (model-scaffold pairs) evaluated 10 times each on SWE-Bench-Verified. Agents are tasked with resolving GitHub issues validated through automated unit tests. Both temperature=0 and production-recommended temperatures were tested.

**Divergence analysis**: Token-level analysis reveals when agentic trajectories split into different solution strategies. Early divergence within the first few percent of tokens proves critical—subsequent deterministic behavior cannot recover from suboptimal early decisions.

**Metric implications**:
- pass@1: Fraction of tasks solved in single attempt (current standard, high variance)
- pass@k: Probability that at least one of k attempts succeeds (optimistic bound)
- pass^k: Probability that all k attempts succeed (pessimistic bound)

Gap between pass@k and pass^k indicates stochastic exploration dependency.

## Recommendations for Practitioners

1. **Multiple independent runs per task** - Especially critical when measuring improvements < 3 percentage points. Single-run evaluations are statistically unreliable for distinguishing signal from noise.

2. **Statistical power analysis** - Determine the required number of runs to detect expected effect sizes before conducting evaluations. Don't rely on ad-hoc single runs.

3. **Report performance envelopes** - Use pass@k (optimistic) and pass^k (pessimistic) alongside pass@1 to characterize the full performance distribution. A single metric masks critical variance information.

4. **Leaderboard implications** - Current rankings and claimed improvements should be interpreted cautiously. Many reported progress may reflect evaluation variance rather than genuine algorithmic advancement.

5. **Cost vs. rigor trade-off** - Multiple runs increase evaluation cost substantially, but this is essential for distinguishing scientific progress from statistical noise. The computational investment is justified for deployment or research direction decisions.

## Implications

This research fundamentally challenges the scientific rigor of current agentic AI evaluation practices. The field has inherited evaluation methodology from code generation (Codex era) without adequately accounting for the increased stochasticity in agentic systems.

For practitioners and researchers:
- **Benchmark results are unreliable as single points**: Any claimed improvement under ~3 percentage points without multiple runs is suspect.
- **Model cards and leaderboards need context**: Published scores lack confidence intervals or variance estimates, making comparative claims dubious.
- **Deployment decisions need scrutiny**: A 2% improvement used to justify billion-dollar model deployments may be within evaluation noise.
- **Research directions may be misguided**: Entire research efforts optimizing for single-run metrics may be chasing statistical artifacts.

The paper argues that this evaluation practice is not just imperfect—it's methodologically unsound. Unlike supervised learning where per-example determinism dominates, agentic systems exhibit fundamental stochasticity that cascades from early decisions. Ignoring this compromises the validity of scientific claims in the field.

## Sources

- [arXiv - On Randomness in Agentic Evals (2602.07150)](https://arxiv.org/html/2602.07150) - Full research paper with experimental methodology and results
- [arXiv Abstract](https://arxiv.org/abs/2602.07150) - Paper abstract and metadata
- [Hacker News Discussion](https://news.ycombinator.com/item?id=46957051) - Community discussion