# My AI Adoption Journey

| | |
|---|---|
| **Source** | Mitchell Hashimoto |
| **URL** | [mitchellh.com/writing/my-ai-adoption-journey](https://mitchellh.com/writing/my-ai-adoption-journey) |
| **Researched** | 2026-02-06 |

## Overview

Mitchell Hashimoto documents his practical AI adoption strategy, moving beyond chatbot interactions to autonomous agents that can verify their own work. The core insight: agent-assisted development isn't about working faster—it's about operating continuously at lower cognitive load, using end-of-day sessions and background processes to amplify otherwise unproductive time.

## Key Points

- **Agents > Chatbots**: Chatbots alone have limited utility in engineering because they require manual verification of every output. Effective systems need agents that can read files, execute programs, test results, and apply fixes autonomously.

- **Verification Loops Beat Speed**: The highest-leverage optimization isn't faster generation—it's automated feedback loops. When agents can verify their own work, "more often than not fixes its own mistakes and prevents regressions."

- **Time-Shifting, Not Time-Stretching**: Rather than expecting agents to compress your working day, deploy them asynchronously—deep research at 5pm, triage queuing overnight, warm context at morning standup. This captures otherwise-lost productivity windows.

- **Know Your Failure Modes**: Maintaining an `AGENTS.md` failure log isn't busywork—it's your operating manual. Each documented failure prevents recurrence and trains your judgment about what tasks to delegate.

- **Context Control Matters**: Turn off agent notifications. Humans should decide when to interrupt focus, not algorithms chasing optimization metrics.

## Technical Details

**Implementation Harness**

The practical infrastructure consists of:

- **Reproducible task execution**: Force yourself to run tasks via agents alongside manual work to genuinely understand capability gaps, not theorize them
- **Verification tooling**: Custom scripts that agents invoke for testing, validation, and rollback—essentially treating agents as client code with a test suite
- **Documentation as feedback**: The `AGENTS.md` failure log creates a learnable architecture of what works in your specific context (codebase, domain, workflows)
- **Operational tempo**: Target 10-20% of work through agents initially, growing as confidence in failure modes increases

Hashimoto's approach treats agent integration like distributed system design: assume failures will occur, build observability, create feedback paths, and iterate on the contract between human judgment and autonomous execution.

## Implications

**For Engineering Leaders**

This framework reveals AI adoption as a *skill-building problem*, not a productivity problem. Three architectural decisions emerge:

1. **Shift task selection criteria**: Stop asking "Can an agent do this faster?" Start asking "Can an agent verify this autonomously?" The latter compounds—humans review exceptions, not every output.

2. **Accept skill trade-offs**: Delegating routine work to agents means expertise in those areas atrophies. Design your AI deployment to preserve growth in core judgment areas while automating administrative patterns.

3. **Build the harness before scaling**: Don't deploy agents across your team without first documenting failure modes in single-person use. Teams need shared understanding of when delegation succeeds or creates churn.

**When This Model Breaks**

This approach assumes:
- Your work has repeatable patterns (creative one-offs, client negotiations, novel architecture decisions resist automation)
- You can articulate verification criteria (fuzzy acceptance tests undermine feedback loops)
- Your codebase and APIs are stable enough for agents to learn patterns (high-churn systems require constant recalibration)

The trade-off: agents excel at parallel exploration and artifact generation but struggle with judgment calls requiring organizational context or stakeholder alignment.

## Sources

- [mitchellh.com/writing/my-ai-adoption-journey](https://mitchellh.com/writing/my-ai-adoption-journey) - Mitchell Hashimoto's personal account of agent-based AI adoption strategy
