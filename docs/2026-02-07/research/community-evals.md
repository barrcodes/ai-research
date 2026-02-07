# Community Evals: Open LLM Evaluation Framework

| | |
|---|---|
| **Source** | Hugging Face Blog |
| **URL** | [huggingface.co/blog/community-evals](https://huggingface.co/blog/community-evals) |
| **Researched** | 2026-02-07 |

## Overview

Community Evals is Hugging Face's decentralized evaluation infrastructure that aggregates LLM benchmark results from multiple sources (model repos, papers, community submissions) into a single transparency layer. Rather than centralized black-box leaderboards, it treats evaluation data as a distributed Git-tracked resource where benchmarks pull results from model repositories and community PRs, eliminating score fragmentation across sources.

## Key Points

- **Architecture**: Three-way system—benchmarks define reproducible evaluation specs (Inspect AI format), models publish results to `.eval_results/` YAML files, community submits PRs with scores and sources. The Hub automatically aggregates and displays results in benchmark datasets and model cards.

- **Decentralized Submission**: Any user submits evaluation results via pull request without model author approval. Results are labeled "community" with full source attribution and Git history. Model owners can close or hide results if needed.

- **Reproducibility via Specification**: Benchmarks register with `eval.yaml` specs based on Inspect AI standards, enabling transparent verification and reducing the "different sources report different scores" problem.

- **API-Driven**: Hub APIs expose all scores, enabling third-party dashboards and curated leaderboards. This inverts control—the community builds on top of the shared data layer rather than competing with centralized platforms.

## Technical Details

**Storage Pattern**: Results in `model_repo/.eval_results/benchmark_name.yaml` with fields for benchmark name, model identifier, scores dictionary, and source URL. Benchmarks automatically query these directories and dataset repos to build leaderboards.

**Initial Coverage**: MMLU-Pro, GPQA, HLE (Hugging Face Leaderboard Evaluation). Additional benchmarks can register without Hub engineering overhead.

**Transparency Mechanism**: Git history shows when evals were added, modified, or removed—creating an audit trail that exposes gaming or corrections. PR discussions allow peer review before scores are merged.

## Implications

**Architectural Shift**: This moves evaluation from a "centralized gatekeeper" model (closed leaderboards, single source of truth curated by one org) to a "data plane + application plane" separation (Hugging Face operates the Hub infrastructure; evaluation authority is distributed).

**For Model Developers**: Eliminates friction of submitting to multiple leaderboards—publish once to your model repo, let Hub aggregation handle distribution. Enables continuous evaluation updates without waiting for leaderboard maintainer approval.

**For Researchers**: Solves a real fragmentation problem—different papers cite different scores for the same model on the same task. Community Evals becomes the reference layer that links to those papers and surfaces contradictions.

**Trade-off**: Decentralization creates coordination overhead. Benchmarks must standardize specs via Inspect AI. Community submissions need moderation policies to avoid spam or deliberately wrong scores. The "who decides if a score is valid" question shifts from platform maintainers to benchmark owners and community discussion.

## Sources

- [Hugging Face Blog: Community Evals](https://huggingface.co/blog/community-evals) - Official announcement and technical specification
- [Inspect AI Framework](https://inspect.aisi.org.uk/) - Evaluation specification standard used by Community Evals
