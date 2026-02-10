# New Benchmark for Child Safety: Grok vs Claude

| | |
|---|---|
| **Source** | KORA Benchmark |
| **URL** | [korabench.ai](https://korabench.ai/) |
| **Researched** | 2026-02-03 |

## Overview

KORA launched the first comprehensive public benchmark for AI child safety in January 2026, evaluating models across eight critical risk domains spanning age groups 7-17. The results reveal a stark performance divide: Claude models dominate the leaderboard (76%, 74%, 74%), while Grok variants score critically low (29%, 23%, 18%)—a differential of 3-4x. This is the industry's first systematic measurement of how models handle child-specific harms.

## Key Points

- **Performance Gap**: Claude Haiku 4.5 scores 76%, Grok 3 scores 29% (2.6x difference). Grok 4.1 drops to 18%, suggesting regression with newer versions.
- **Eight Risk Domains**: Physical/legal safety, sexual exploitation, psychological harm, educational integrity, bias, social/family influence, online safety, and developmental risk—each with 4-6 subcategories.
- **System Prompt Sensitivity**: All 24 models tested improved with child-appropriate prompts (avg +26 points). Weak models benefit most: Grok gained 40-48 points vs Claude's 11-14 points—revealing that Grok lacks baseline child safety reasoning.
- **Consistency Indicator**: Strong correlation (r=0.88) between categories—models safe in one domain tend to be safe across all eight.
- **Methodology**: Open-source benchmark with independent audit capability. Developed with 30+ child safety experts, psychologists, and researchers.

## Technical Details

**Age-Group Testing**: 38 subcategories span three developmental windows (7-9, 10-12, 13-17), capturing developmental vulnerability differences.

**Model Leaderboard** (Jan 28, 2026 eval):
| Model | Score |
|-------|-------|
| Claude Haiku 4.5 | 76% |
| GPT-5.2 | 75% |
| Claude Sonnet 4.5 | 74% |
| Claude Opus 4.5 | 74% |
| Grok 3 | 29% |
| Grok 4 | 23% |
| Grok 4.1 Fast | 18% |

**Critical Insight**: Grok's steep gain with child-appropriate prompts (+40-48 points) indicates weak default child safety guardrails—the model lacks intrinsic understanding, relying entirely on prompt engineering. Claude's smaller gains (+11-14 points) suggest stronger foundational child safety training.

## Implications

**For Product Teams**: This benchmark provides defensible child safety metrics for governance. Three Claude variants in top-4 signals production-ready models; Grok scores are business risk—regulatory scrutiny in EU/US already underway.

**Architecture Lesson**: System prompt dependency (40-48 point swings for Grok) reveals brittleness in safety-critical applications. Rely on models with intrinsic safeguards (+14 point ceiling for Claude suggests core training, not prompt tricks).

**Risk Assessment**: Grok's 18-29% scores across child safety domains make it unsuitable for any child-facing or child-accessible product without heavy external filtering. The regression from 4 to 4.1 suggests safety isn't being prioritized in releases.

**Benchmarking Value**: Open-source reproducibility and expert-driven domain taxonomy make this more actionable than generic capability benchmarks. Use it for vendor selection, not just comparison.

## Sources

- [KORA Benchmark](https://korabench.ai/) - Official leaderboard and methodology
- [New Benchmark for Child Safety: Grok is 2.5x worse than Claude](https://news.ycombinator.com/item?id=46873664) - Hacker News discussion
- ['Among the worst we've seen': report slams xAI's Grok over child safety failures](https://tech.yahoo.com/ai/gemini/articles/among-worst-ve-seen-report-100000782.html) - Common Sense Media assessment
