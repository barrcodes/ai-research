---
title: Which LLM writes the best R code?
source: Posit / Simon P. Couch Blog
url: https://www.simonpcouch.com/blog/2025-04-15-gpt-4-1/
researched_at: 2026-01-27T00:00:00Z
---

# Which LLM writes the best R code?

## Overview

Posit's research using the **vitals** package—an R evaluation framework—benchmarked five LLMs on R coding tasks. Claude 3.7 Sonnet consistently outperformed all OpenAI models including the newer GPT-4.1 series. The work introduces a rigorous evaluation methodology for assessing LLM code generation capabilities specific to R, addressing a gap in domain-specific benchmarking.

## Key Points

- **Winner**: Claude 3.7 Sonnet outperforms GPT-4o, GPT-4.1, GPT-4.1 mini, and GPT-4.1 nano on R coding tasks
- **Evaluation framework**: vitals package (R port of Python's Inspect framework) with 26 challenging R problems evaluated across 3 epochs = 390 observations
- **Scoring methodology**: Model-graded assessment using Claude Sonnet 3.7 as judge, categorizing responses as Incorrect, Partially Correct, or Correct
- **Statistical rigor**: Cumulative link mixed models account for question difficulty variation and non-independent observations
- **Cost-efficiency finding**: GPT-4.1 nano demonstrated impressive performance-per-dollar despite lower absolute accuracy

## Technical Details

| Model | Category |
|-------|----------|
| Claude 3.7 Sonnet | Best overall |
| GPT-4o | Strong baseline |
| GPT-4.1 | Improved baseline |
| GPT-4.1 mini | Mid-range cost |
| GPT-4.1 nano | Best value proposition |

**Dataset**: "An R Eval" (ARE) — 26 real-world R programming problems with labeled solutions spanning realistic coding scenarios

**Evaluation pipeline**:
1. **Dataset**: Problems with expected solutions
2. **Solver**: `generate()` function calling each model's chat API
3. **Scorer**: Model-graded comparison with ordinal outcomes (Incorrect/Partial/Correct)

**Statistical model**: Cumulative link mixed models to handle ordinal response structure and repeated measures across epochs

## Implications

**For practitioners choosing coding assistants:**
- If R code quality is critical, Claude Sonnet remains the performance leader, justifying its cost premium for production code generation
- GPT-4.1 nano offers viable cost trade-offs for lower-stakes use cases (documentation, exploratory code) despite 10-15% accuracy gap
- Model-grading evaluation using LLMs themselves provides scalable assessment without human annotation; consider this approach for custom code benchmarking

**Architectural considerations:**
- vitals framework enables programmatic evaluation of LLM products in R workflows—relevant for teams building copilots, query assistants, or code generation tools
- Evaluation dataset size (26 problems) is modest but statistically rigorous through multi-epoch design; benchmark more domain-specific subsets for specialized tooling decisions
- Ordinal scoring (partial credit) captures realistic failure modes better than binary correctness metrics

**Research direction**: R-specific benchmarking remains underdeveloped compared to general-purpose code benchmarks (HumanEval, SWE-bench); domain-specific evaluation frameworks unlock better model selection for specialized languages.

## Related

- [vitals package documentation](https://vitals.tidyverse.org/) - R evaluation framework source
- [Introducing vitals 0.1.0](https://tidyverse.org/blog/2025/06/vitals-0-1-0/) - Framework announcement and design rationale
- [ellmer package](https://ellmer.tidyverse.org/) - R interface for LLMs, works with vitals for evaluation
- [Inspect (Python)](https://github.com/alan-turing-institute/inspect_ai) - Original evaluation framework that vitals ports from
