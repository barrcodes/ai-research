# Claude is a Space to Think

| | |
|---|---|
| **Source** | Anthropic Research |
| **URL** | [anthropic.com/news/visible-extended-thinking](https://www.anthropic.com/news/visible-extended-thinking) |
| **Researched** | 2026-02-04 |

## Overview

Anthropic's Claude 3.7 Sonnet introduces extended thinking—a test-time compute scaling mechanism enabling models to allocate variable reasoning effort before responding. Rather than switching models, the same weights spend more sequential tokens on complex problems. This breaks through majority-voting scaling limits on reasoning tasks, with quantifiable performance improvements across mathematics, physics, and agentic tool use.

## Key Points

- **Configurable compute allocation**: Developers control "thinking budget" (token allocation), trading latency for quality without model swaps
- **Logarithmic scaling on hard problems**: AIME math accuracy improves predictably with token budgets; physics reaches 96.5% on GPQA
- **Same architecture, different behavior**: Extended thinking doesn't require separate specialized models—inference-time control changes reasoning depth
- **Parallel sampling enables voting**: Multiple independent thought processes sampled simultaneously, with majority voting or learned scoring selecting best responses
- **Visible reasoning reveals blindspots**: Raw thought processes expose incomplete reasoning, helping identify confidence limitations in safety-critical domains

## Technical Details

Extended thinking implements serial test-time compute scaling—sequential reasoning tokens before output generation. Performance characteristics:

| Benchmark | Result | Notes |
|---|---|---|
| GPQA (256 samples, 64k budget) | 84.8% | Scales past voting plateaus |
| Physics subscore | 96.5% | Specialized domain breakthrough |
| AIME math questions | Logarithmic improvement | Predictable with token allocation |
| OSWorld (computer use) | Improved vs. predecessor | Multi-step task automation |

The visible thinking process reveals "incorrect, misleading, or half-baked thoughts"—unfiltered reasoning. This transparency exposes a critical safety limitation: models make decisions on factors they don't explicitly discuss, complicating confidence assessment in adversarial or safety-critical applications.

## Implications

**For practitioners**: Extended thinking shifts optimization from architecture to inference-time configuration. Team decisions now include thinking budget allocation during request time—adding latency for reliability. This makes sense for batch processing (reports, analysis, complex planning) but not low-latency APIs. Parallel sampling with voting moves reasoning from single-path to ensemble patterns, similar to test-time scaling in vision models.

**For agents**: Multi-step tool calls benefit significantly. OSWorld results suggest that agentic patterns—where Claude chains function calls—improve with extended thinking, making it critical for automation workflows that handle planning, error recovery, and dynamic task decomposition.

**Trade-off**: Visible reasoning doesn't guarantee explainability. Anthropic acknowledges safety blind spots where models act on unstated factors. For high-stakes decisions, visible thinking reduces opacity but doesn't eliminate it—pair with external verification systems.

## Sources

- [Anthropic: Claude's extended thinking](https://www.anthropic.com/news/visible-extended-thinking) - Technical announcement with benchmarks
- [The "think" tool documentation](https://www.anthropic.com/engineering/claude-think-tool) - Complementary runtime control mechanism
- [Platform Claude API docs: Extended thinking](https://platform.claude.com/docs/en/build-with-claude/extended-thinking) - Implementation guide
