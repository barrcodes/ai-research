# Show HN: AI Wattpad to eval LLMs on fiction

| | |
|---|---|
| **Source** | Narrator.sh / Hacker News |
| **URL** | [narrator.sh/llm-leaderboard](https://narrator.sh/llm-leaderboard) |
| **Researched** | 2026-02-03 |

## Overview

Narrator.sh introduces a novel LLM evaluation framework that replaces synthetic benchmarks with real reader engagement metrics for fiction writing. The platform generates serialized chapters using DSPy with reward-weighted optimization, feeding actual reader behavior (time spent, ratings, bookmarks, comments, return visits) back into model selection and parameter tuning rather than relying on token prediction accuracy or human raters.

## Key Points

- **Real-world signal over benchmarks**: Engagement metrics (dwell time, bookmarks, comments) outweigh traditional eval metrics for fiction quality assessment
- **Multi-stage generation pipeline**: Separate models for ideation (brainstorming), execution (writing), and context management (memory), optimized independently
- **DSPy-based optimization**: Uses SIMBA optimizer to recompile user ratings from previous chapters, enabling adaptive parameter tuning across generations
- **Sampling parameters matter**: High-temperature exploration with advanced truncation (min_p, mirostat) significantly improves narrative quality in ways standard evals miss
- **Technical errors are primary failure vector**: Current engagement bottlenecks stem from repetition, grammatical errors, and plot inconsistencies—not subjective writing quality

## Technical Details

The architecture decomposes creative writing into three specialized sub-tasks:

| Component | Purpose | Optimization |
|---|---|---|
| **Brainstorming Model** | Plot, worldbuilding, concept generation | LLM-as-judge reward function |
| **Writer Model** | Chapter production, narrative execution | SIMBA optimizer using user ratings |
| **Memory Model** | Context retrieval, continuity maintenance | Long-context retrieval over prior chapters |

The evaluation loop captures engagement signals that traditional benchmarks miss: reader dropout points, chapter completion rates, and community feedback. Each chapter's reader responses inform the next generation's parameters, creating a feedback loop that tunes for actual reader satisfaction rather than synthetic metrics.

## Implications

This approach highlights a critical gap in current LLM evaluation: benchmarks optimize for token prediction, but creative writing success depends on reader retention and narrative coherence. For practitioners:

- **When to adopt**: If you're building content platforms where user engagement is the business metric (Wattpad, serialized fiction, interactive narratives), real engagement data is more predictive than MMLU or GSM8K scores
- **Sampling matters more than we thought**: The discussion revealed that creative tasks benefit disproportionately from temperature and sampling strategy tuning—parameter configuration can outweigh base model capability
- **Personalization is the next frontier**: Once technical errors diminish, the challenge shifts to codifying user taste preferences, suggesting recommendation/fine-tuning approaches similar to Midjourney's style integration
- **Trade-off**: Real engagement evals are slower to iterate on (require actual users/chapters) but yield more actionable data than benchmark suites; useful for specialized domains but impractical for general-purpose model selection

## Sources

- [narrator.sh LLM Leaderboard](https://narrator.sh/llm-leaderboard) - Platform implementing reader-engagement-based evaluation
- [Show HN: Evaluating LLMs on creative writing via reader usage, not benchmarks](https://news.ycombinator.com/item?id=44903265) - Technical discussion of DSPy implementation and sampling parameters
