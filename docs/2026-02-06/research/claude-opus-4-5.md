# Introducing Claude Opus 4.5

| | |
|---|---|
| **Source** | Anthropic |
| **URL** | [anthropic.com/news/claude-opus-4-5](https://www.anthropic.com/news/claude-opus-4-5) |
| **Researched** | 2026-02-06 |

## Overview

Anthropic released Claude Opus 4.5, claiming state-of-the-art performance across coding, agentic reasoning, and computer use tasks. The release introduces an "effort parameter" that allows developers to trade compute for capability—achieving 76% token reduction at medium effort with performance parity to Sonnet 4.5, or exceeding Sonnet by 4.3 points at full effort with 48% fewer tokens. Pricing drops to $5/$25 per million tokens (input/output).

## Key Points

- **SWE-bench domination**: Leads on SWE-bench Verified and 7 of 8 programming languages in multilingual benchmarks; 10.6% improvement over Sonnet 4.5 on polyglot coding tasks
- **Agentic capabilities**: 29% improvement on Vending-Bench for long-horizon task completion; significant gains on frontier agentic search (BrowseComp-Plus)
- **Token efficiency breakthrough**: Effort parameter decouples model compute from task complexity—developers can calibrate inference cost/latency without switching models
- **Safety posture**: Most robustly aligned Anthropic model to date with enhanced defenses against prompt injection attacks
- **Accessibility**: Lower pricing tier ($5/$25) compared to previous Opus versions

## Technical Details

The effort parameter represents the architecturally significant innovation here. Rather than forcing a single inference compute budget, Opus 4.5 supports variable token allocation per request. At medium effort, it uses 76% fewer output tokens while maintaining Sonnet 4.5 performance parity. At highest effort, it exceeds Sonnet by 4.3 percentage points while still consuming 48% fewer tokens. This suggests the model's underlying reasoning depth is uncoupled from token output through improved planning or compression strategies.

| Category | Result |
|----------|--------|
| SWE-bench Verified | State-of-the-art |
| Programming languages (multilingual) | 7 of 8 leads |
| Aider Polyglot vs Sonnet 4.5 | +10.6% |
| Vending-Bench (long-horizon) vs Sonnet 4.5 | +29% |
| Output tokens at medium effort | -76% vs Sonnet 4.5 |
| Output tokens at full effort | -48% vs Sonnet 4.5 |

## Implications

**For coding teams**: This is the clear default for software engineering automation, agent development, and long-task completion. The token efficiency gain is material—if your codebase uses Claude for code generation or review, switching to Opus 4.5 with medium effort settings could cut inference costs by ~50% while maintaining quality.

**For agentic systems**: The 29% improvement on long-horizon tasks makes it viable for more complex automation workflows. The robustness improvements suggest lower failure rates for production agent deployments.

**For cost-optimized workloads**: The effort parameter eliminates the need to downgrade to cheaper models for latency-sensitive or cost-sensitive requests. This reduces operational complexity—single model, tuned via parameter rather than version switching.

**Architectural trade-off**: Effort parameter adds inference-time optimization surface. Teams must decide per-use-case whether to maximize speed (low effort), cost (medium effort), or capability (high effort). This introduces configuration complexity but aligns incentives: pay more tokens only when the task demands them.

## Sources

- [Anthropic Blog - Introducing Claude Opus 4.5](https://www.anthropic.com/news/claude-opus-4-5) - Official release announcement with benchmarks
