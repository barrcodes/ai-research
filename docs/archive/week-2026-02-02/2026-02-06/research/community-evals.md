# Community Evals: Because we're done trusting black-box leaderboards

| | |
|---|---|
| **Source** | Hugging Face Blog |
| **URL** | [huggingface.co/blog/community-evals](https://huggingface.co/blog/community-evals) |
| **Researched** | 2026-02-06 |

## Overview

Hugging Face introduces a decentralized, transparent evaluation system that replaces opaque benchmark leaderboards with community-driven assessments. The framework makes evaluation methodology, timing, and reproducibility visible by storing standardized specs in dataset repos and aggregating results from both model authors and community contributors via pull requests.

## Key Points

- **Benchmark Saturation Exposed**: Models achieve 91%+ on MMLU and 94%+ on GSM8K yet fail at real-world tasks—standard leaderboards don't capture production capability
- **Fragmented Truth Problem**: Same models report different scores across papers, Model Cards, and platforms; no authoritative source exists
- **Three-Layer Architecture**: Benchmarks define eval specs in YAML; models store results in `.eval_results/*.yaml`; community submits via PRs without merge requirement
- **Inspect AI Integration**: Uses existing Inspect format for eval specifications, enabling reproducibility and cross-platform execution
- **Git-Based Provenance**: Full history tracks who submitted scores, when, and links to supporting evidence (papers, logs, Model Cards)

## Technical Details

The system uses YAML-based evaluation specifications matching Inspect AI format, stored alongside benchmark datasets. Results flow bidirectionally: model repos reference benchmark scores, and benchmark dataset cards aggregate submissions from both official model authors and community evaluators. Community PRs appear as non-merged contributions, maintaining transparency without requiring gatekeeping.

All scores expose via Hub APIs, enabling custom dashboards and third-party aggregations. The architecture decouples evaluation methodology (spec) from execution (any framework), reducing vendor lock-in while standardizing what's being measured.

## Implications

**For evaluation practitioners**: This shifts the game from "which leaderboard should I trust?" to "what methodology was used, and can I verify it?" It pressures model authors toward reproducible evaluation practices and enables detecting score manipulation or methodology gaming.

**For model development**: The community submission path reduces friction for researchers to surface alternative evaluations (domain-specific, adversarial, real-world stress tests) without waiting for official inclusion. However, this creates interpretation complexity—how do practitioners weight community vs. author-submitted results?

**Trade-off**: Transparency gains increase cognitive load in evaluation selection. The leaderboard fragmentation problem persists, now with added opacity around which evaluations practitioners should prioritize. This requires establishing community norms for evaluation quality and relevance.

## Sources

- [Hugging Face Blog: Community Evals](https://huggingface.co/blog/community-evals) - Announcement of decentralized evaluation framework
- [Inspect AI](https://inspect.aisi.org.uk/) - Evaluation specification format used in system
