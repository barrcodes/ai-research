---
title: Introducing Claude Sonnet 4.5
source: Anthropic
url: https://www.anthropic.com/news/claude-sonnet-4-5
researched_at: 2026-01-26T00:00:00Z
---

# Introducing Claude Sonnet 4.5

## Overview

Claude Sonnet 4.5 delivers substantial improvements in coding ability (77.2% on SWE-bench Verified, reaching 82% with extended compute), autonomous task execution (61.4% on OSWorld), and domain-specific reasoning across finance, law, medicine, and STEM. Anthropic now offers the Claude Agent SDK—the same infrastructure powering Claude Code—to all developers, enabling autonomous agents with memory management, permission systems, and subagent coordination.

## Key Points

- **Coding dominance**: Zero error rate on code editing benchmarks vs 9% for Sonnet 4; sustains focus for 30+ hours on complex multi-step tasks
- **Agentic capabilities**: 45% improvement on OSWorld (61.4% vs 42.2% four months prior); parallel tool execution and stronger prompt injection defense
- **Claude Agent SDK**: Public release of enterprise-grade agent infrastructure—memory, permissions, subagent coordination—at Sonnet 4 pricing ($3/$15 per million tokens)
- **Safety leadership**: 10x reduction in false positives on CBRN classifiers; mechanistic interpretability testing included; reduced deception, sycophancy, and power-seeking behaviors
- **Domain expertise**: Dramatic improvements in specialized knowledge across finance, law, medicine, and STEM

## Technical Details

| Metric | Sonnet 4.5 | Sonnet 4 | Delta |
|--------|-----------|---------|-------|
| SWE-bench Verified | 77.2% | ~62% | +24% |
| SWE-bench (extended) | 82.0% | — | — |
| OSWorld | 61.4% | 42.2% | +45% |
| Code editing errors | 0% | 9% | -9pp |

**Context window trade-off**: 200K context yields primary scores (optimized post-inference stability); 1M context achieves 78.2% on SWE-bench. Developers choose based on latency/accuracy priorities.

**Agentic infrastructure**: Memory spans long-running tasks; permission systems balance autonomy with user control; checkpoint-based rollback enables safe autonomous execution in VS Code extension.

## Implications

**For engineering teams**: Upgrade universally across applications, APIs, and Code interfaces. The 82% SWE-bench (extended) score makes Sonnet 4.5 the default for complex refactoring and multi-file edits. The 45% gain on OSWorld changes feasibility calculus for autonomous agents—previously edge-case, now production-ready.

**For agent builders**: Claude Agent SDK democratizes infrastructure that was proprietary to Claude Code. Permission boundaries and memory management reduce implementation burden; subagent coordination enables hierarchical autonomy patterns. Same pricing removes upgrade friction.

**Trade-off decisions**: Evaluate context window for your workload—200K provides stability and responsiveness; 1M trades latency for marginally better reasoning on massive codebases. For interactive experiences, 200K is the production choice.

**Safety for autonomous ops**: Improved prompt injection defense and CBRN classifier accuracy (10x false positive reduction) make extended autonomous execution lower-risk, but still warrant user checkpoints on high-stakes actions.

## Related

- [Claude Code Extension](https://marketplace.visualstudio.com/items?itemName=Anthropic.claude) - Native VS Code integration with checkpoints
- [Claude Developer Platform](https://console.anthropic.com) - SDK access and API endpoints
- [System Card](https://www.anthropic.com/research/research-papers) - Safety evaluations and mechanistic interpretability details
