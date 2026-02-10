# AIME 2026: Open and Closed Models Cross 90% Threshold

| | |
|---|---|
| **Source** | LLM Benchmarks (Artificial Analysis, WhatLLM, Swfte) |
| **URL** | [artificialanalysis.ai/evaluations/aime-2025](https://artificialanalysis.ai/evaluations/aime-2025) |
| **Researched** | 2026-02-07 |

## Overview

Both proprietary and open-source models now routinely exceed 90% accuracy on the AIME (American Invitational Mathematics Examination) 2025 benchmark, marking a critical inflection point in LLM mathematical reasoning. Frontier models achieve near-perfect scores (100%), while open models like Llama 4 Maverick and Qwen3 variants hit 90%+ ranges. This convergence signals that math reasoning—once a differentiator between closed and open models—has commoditized.

## Key Points

- **Frontier closed models:** GPT-5.2 (100%), Gemini 3 Flash (97%), Claude Opus 4.5 (92%) dominate the 90%+ club
- **Open models breaking through:** Llama 4 Maverick (19.3%), Qwen3-235B-A22B (91%), gpt-oss variants (89-93%) now competitive with mid-tier proprietary models
- **Reasoning models dominate:** 9 of the top 10 models are explicit reasoning variants—test-time compute has become the primary performance lever
- **Cost-performance inversion:** Open source models achieve 90% of proprietary performance at 7-20x lower inference cost
- **Benchmark saturation emerging:** Models approaching 99%+ on AIME signal ceiling effects—new evaluation methodology may be needed

## Technical Details

### Proprietary Model Results (AIME 2025)
- **GPT-5.2 (xhigh):** 100% (960k reasoning tokens, $6 cost)
- **Gemini 3 Flash:** 97% (820k reasoning tokens)
- **MiMo-V2-Flash:** 96.3%
- **Gemini 3 Pro Preview (high):** 95.7%
- **GLM-4.7:** 95% (reasoning model)
- **Kimi K2 Thinking:** 94.7%
- **gpt-oss-120B (high):** 93.4%

### Open-Source Model Results
- **Qwen3-235B-A22B:** 91% on AIME 2025 (down from 92.3% reported in some sources), MoE architecture, 22B active parameters per token
- **Klear-Reasoner 8B:** 90.5% on AIME 2024 → 83.2% on AIME 2025 (notable regression year-over-year)
- **Llama 4 Maverick:** 19.3% baseline (appears to be non-reasoning variant on this leaderboard)
- **Mistral Large 3:** 76.7%
- **DeepSeek V3.2:** 92.7% (open-weight)

### Architecture Patterns
**Reasoning models dominate the top 10:** GPT-5.2, Gemini 3 Flash/Pro, MiMo-V2, GLM-4.7, Kimi K2 are all explicit reasoning variants. The shift from base to reasoning-based evaluation represents a fundamental change in how LLMs approach math:

- **Test-time compute:** Reasoning models consume 600k-960k tokens per problem vs. standard models using <50k tokens
- **Pass@N sampling:** Models achieving high accuracy through multiple reasoning chains with filtering (DeepConf achieves 99.9% via confidence-based trace filtering on AIME 2025)
- **Token cost trade-off:** Frontier reasoning models cost $5-6 per 30-problem AIME run vs. standard models at $0.05-0.20

### Inference Economics
- **DeepSeek R1:** $0.07/million cached tokens (94% cheaper than Claude Opus 4.5 at $15/million)
- **Self-hosting 70B models:** Breakeven at ~2M tokens/day with 70%+ GPU utilization on consumer GPUs (dual RTX 5090 = $4k upfront vs. $3.90/hour cloud H100)
- **MMLU gap collapse:** Open-closed gap narrowed from 17.5 percentage points (2024) to 0.3 points (December 2025)

## Implications

### For Practitioners
1. **Math reasoning commoditized:** If AIME performance is your primary selection criterion, open-source models now offer 80-90% of frontier capability at 10x cost advantage. Reason to optimize inference path: test-time compute budgets matter more than base model choice.

2. **Benchmark ceiling approaching:** The 90%+ saturation across diverse architectures suggests AIME may not differentiate models effectively in 2026. New evaluation methodologies for math reasoning are needed (consider multi-step proof checking, error detection, or partial credit schemes).

3. **Reasoning as inference commodity:** The shift to explicit reasoning models (with 600k+ tokens per problem) makes inference cost predictable but substantial. For production deployments, this requires:
   - Explicit reasoning budget tracking
   - Fallback to faster models for low-confidence cases
   - Potential fine-tuning of simpler models on domain-specific problems

4. **Closed models remain relevant only for lowest-latency requirements:** If sub-second math answers are critical (interactive applications), proprietary models' streaming capabilities matter. For batch processing, cost/performance curves favor open models.

### For Architecture Decisions
- **Self-hosting becomes viable:** The 6-12 month payback period for on-premise 70B models applies to any team processing >2M math tokens daily. This unlocks data sovereignty for regulated sectors (finance, healthcare).
- **MoE efficiency gains material:** Qwen3 and gpt-oss variants using sparse attention (50% efficiency gains reported) suggest MoE becomes standard for cost-conscious deployments.
- **Reasoning models require infra changes:** Standard serving frameworks (vLLM, etc.) now must handle 600k+ context windows efficiently. Failure to optimize here creates 10-100x cost penalties.

### Market Dynamics
- **Proprietary model differentiation shrinking:** Cost advantage alone (94% savings) may drive open-source adoption for math tasks in enterprise, despite latency penalties
- **Test-time compute licensing emerging:** Future revenue from reasoning models may shift from base inference to reasoning token pricing (OpenAI's $20/month GPT-4 with reasoning use is early indicator)
- **Specialized models fragmentation:** Domain-specific models (Mathstral, Devstral) now more viable due to open-source accessibility

## Sources

- [Artificial Analysis AIME 2025 Leaderboard](https://artificialanalysis.ai/evaluations/aime-2025) - Comprehensive evaluation of 24 models with token/cost breakdown
- [Swfte AI: Open Source Models Rival Proprietary Giants (2026)](https://www.swfte.com/blog/open-source-ai-models-frontier-2026) - Cost/economics analysis with specific AIME scores
- [WhatLLM: Best Math LLMs January 2026](https://whatllm.org/blog/best-math-models-january-2026) - Comparative ranking of math-specific models
- [LLM Stats: AIME Leaderboard](https://llm-stats.com/benchmarks/aime) - Real-time leaderboard tracking
