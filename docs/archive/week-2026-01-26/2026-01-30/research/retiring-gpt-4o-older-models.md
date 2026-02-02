# Retiring GPT-4o, GPT-4.1, GPT-4.1 mini, and OpenAI o4-mini in ChatGPT

| | |
|---|---|
| **Source** | OpenAI |
| **URL** | [openai.com/index/retiring-gpt-4o-and-older-models/](https://openai.com/index/retiring-gpt-4o-and-older-models/) |
| **Researched** | 2026-01-30 |

## Overview

OpenAI is consolidating its model lineup by retiring four older model variants from ChatGPT on February 13, 2026, while concurrently retiring API access on February 16-17, 2026. The consolidation reflects OpenAI's focus on GPT-5.2 adoption—which now represents 99.9% of daily ChatGPT users—and simplifies the model portfolio for both users and platform operators.

## Key Points

- **ChatGPT Sunset**: GPT-4o, GPT-4.1, GPT-4.1 mini, and o4-mini removed from ChatGPT on February 13, 2026
- **API Deprecation**: Access ends February 16-17, 2026 for developers (announced November 18, 2025)
- **Usage Data**: Only 0.1% of ChatGPT users actively select GPT-4o daily; majority migrated to GPT-5.2
- **Partial Preservation**: GPT-4o variants with specialized capabilities (Transcribe, TTS) remain in service
- **Azure Timeline**: Staggered retirement through June 2026; older GPT-4o versions retire March 31, 2026; GPT-4.1 suite retires October 14, 2026
- **Replacement Path**: GPT-5.1 for GPT-4o; GPT-5-mini for GPT-4.1-mini; o3 for o1

## Technical Details

| Model | ChatGPT Sunset | API Retirement | Azure End Date | Replacement |
|-------|---|---|---|---|
| GPT-4o (2024-05-13) | 2/13/26 | 2/16/26 | 3/31/26 | GPT-5.1 |
| GPT-4o (2024-08-06) | 2/13/26 | 2/16/26 | 3/31/26 | GPT-5.1 |
| GPT-4o (2024-11-20) | 2/13/26 | 2/16/26 | 6/5/26 | GPT-5.1 |
| GPT-4o-mini | 2/13/26 | 2/16/26 | 3/31/26 | GPT-4.1-mini |
| GPT-4.1 | 2/13/26 | 2/16/26 | 10/14/26 | GPT-5 |
| GPT-4.1-mini | 2/13/26 | 2/16/26 | 10/14/26 | GPT-5-mini |

Azure OpenAI Service offers longer transition windows than ChatGPT—typically 12 months from deprecation to retirement for GA models—providing additional runway for enterprise migrations.

## Implications

**For Practitioners:**

1. **Immediate Action Required**: Production systems using gpt-4o or gpt-4.1 variants need migration paths within 6 weeks for ChatGPT, longer for Azure deployments
2. **Model Selection Trade-off**: Consolidation reduces model choice (simplifies operations) but requires evaluating GPT-5-class alternatives which may have different performance/cost profiles
3. **Batch API Consideration**: Developers should verify whether batch processing endpoints are affected by the deprecation
4. **Testing Window**: Three-week window (Feb 13-17) between ChatGPT and API shutdowns suggests staging migrations to ChatGPT first, then API endpoints
5. **Cost Structure**: GPT-5 models typically have different pricing than GPT-4o; review pricing per 1K tokens before committing migrations
6. **Backward Compatibility**: Applications explicitly specifying model versions (e.g., `gpt-4o-2024-05-13`) will fail immediately; hard-coded model identifiers create brittleness

**When to Migrate Early**: Organizations with complex model evaluation workflows should prioritize early migration testing to GPT-5.1, as 99.9% of users have already completed this transition.

## Related

- [Azure OpenAI Model Retirements](https://learn.microsoft.com/en-us/azure/ai-foundry/openai/concepts/model-retirements?view=foundry-classic) - Comprehensive table of all Azure retirement timelines
- [OpenAI Deprecations Documentation](https://platform.openai.com/docs/deprecations) - Official API deprecation timeline
- [Techmeme Discussion](https://www.techmeme.com/260129/p55) - Community reactions and consolidation rationale
