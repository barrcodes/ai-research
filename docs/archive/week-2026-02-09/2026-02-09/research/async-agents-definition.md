# Everyone's Building 'Async Agents,' But No One Can Define Them

| | |
|---|---|
| **Source** | Multiple industry sources (Omnara, Zed, DevRev, Benjamin Anderson) |
| **URL** | [omnara.com/blog/what-is-an-async-agent-really](https://www.omnara.com/blog/what-is-an-async-agent-really) |
| **Researched** | 2026-02-09 |

## Overview

The industry faces a critical terminology problem: "async agents" simultaneously refers to at least two distinct architectural patterns with minimal overlap, yet practitioners use the terms interchangeably. This conflation costs engineering teams weeks of debugging and poor architectural decisions. As async agents become table stakes (Cognition's Devin, Cursor, OpenAI, Anthropic, Google all ship variants), the lack of semantic clarity creates a category problem—teams cannot evaluate, compare, or implement these systems effectively without shared definitions.

## The Core Definitional Problem

**Two Meanings, One Term:**

The term "async agents" collapses two separate domains:

1. **Concurrency Model** (Systems/Programming): Sequential composition of asynchronous I/O operations. Async is about CPU utilization during waits, not parallelism. A single operation counts as an async agent if it's non-blocking. This definition derives from established concurrency theory (ASIO, C++ concurrency, Node.js patterns).

2. **Agent Execution Model** (AI/Autonomy): Background workers that independently execute time-intensive tasks without continuous user interaction. The agent runs to completion or explicit breakpoint, informing users upon done. This is fundamentally about interaction modality and autonomy, not I/O scheduling.

**The Confusion Cost:** Early teams used these interchangeably, leading to "weeks of debugging and poor architectural decisions." An engineer might discuss async agent orchestration while thinking of task queues, while another thinks of concurrent HTTP requests. Different mental models, same terminology.

## Industry Interpretations

| Perspective | Definition | Use Case | Key Characteristic |
|---|---|---|---|
| **Synchronous AI Agent** | Real-time interactive tools (chatbot-like). Session ends when user leaves. | Customer support, help desk, live coding | High user attention required, context lost on disconnect |
| **Asynchronous AI Agent** | Background task executor. Operates on own timeline, persists context. | Code review, research, debugging, automation | Low user attention, work done while user is away |
| **Cloud/Coding Agent** | LLM with tool use running in managed environment. Combines model + harness + environment. | Bug fixing, feature implementation, testing | Execution environment abstraction, model-agnostic capability |
| **Signal-Quality Agent** | Highly selective intervention tool. Speaks only when high-signal. | Code review augmentation, linting | Restraint and precision over volume |

**The Real Disagreement:** Industry disagrees not on terminology but on what async agents *should do*. Zed's analysis found most agents fail because they generate noise (low-value comments, hallucinated summaries, invasive edits) rather than signal. The exception—Graphite Diamond—succeeds by remaining silent unless detecting genuine issues. This maps to a design philosophy gap, not a technical definition gap.

## Technical Architecture Divergence

**Orchestration vs. Choreography Pattern:**

- **Synchronous Orchestration**: Central supervisor agent coordinating multi-agent workflows. Maintains high-level control, predictable execution order.
- **Asynchronous Choreography**: Event-driven, no central orchestrator. Agents react to state changes autonomously. Workflow emerges from collective behavior.

These are inverse control patterns. A team choosing async needs to clarify which choreography model they're adopting—and this choice cascades through message passing, observability, and failure recovery.

**Implementation Specificity Problem:**

Benjamin Anderson's homebrew async agent system reveals the abstraction gap. His custom architecture includes:
- Slack triggers + GitHub app integration (task ingestion)
- Modal serverless compute (isolation boundary)
- Ephemeral token management (security scoping)
- Real-time log visualization via CCViewer (human oversight)

None of these are "async" in the concurrency sense. Yet this is what practitioners mean by "building an async agent." Enterprise solutions (Devin, Cursor) provide these implementations as black boxes, creating a differentiation barrier based on architectural choices, not definitional clarity.

## Why This Matters for Engineering

**1. Technology Selection Becomes Guesswork**

Without shared definitions, a team evaluating "async agent frameworks" cannot answer:
- Is this solving the concurrency problem or the autonomy problem?
- Should I use orchestration or choreography?
- What observability model does this assume?

Different tools solve different problems under the same label.

**2. Multi-Agent System Design Breaks**

If your team disagrees on whether "async" means "non-blocking I/O" or "background task," designing multi-agent coordination fails. Message passing semantics, timeout handling, and failure recovery all depend on this understanding.

**3. Vendor Lock-in Risk**

Enterprise solutions bury architectural choices inside "async agent" abstractions. Migrating from Devin to Cursor requires re-implementing not just the model swap, but the entire orchestration/choreography pattern. Teams lack vocabulary to articulate this cost.

**4. Observability and Control Gaps**

Anderson's CCViewer system exists because enterprises don't provide real-time visibility into background agent execution. Teams need to overthink human-in-the-loop integration (approval gates, edit authority) because async agents conflate "independent execution" with "unsupervised execution."

## Proposed Clarity

**Semantic Distinction Framework:**

| Dimension | Question | Spectrum |
|---|---|---|
| **Interaction** | How does the user trigger work? | Explicit invocation ↔ Event-triggered |
| **Autonomy** | Does agent run to completion or pause? | Run-to-done ↔ Pause-on-ambiguity |
| **Concurrency** | How do multiple agents coordinate? | Orchestrated ↔ Choreographed |
| **Observability** | Can humans intervene mid-execution? | No ↔ Yes (breakpoints, approvals) |
| **Isolation** | What resources can the agent access? | Restricted ↔ Full environment |

**Better Terminology Candidates:**

- **"Background Agent"** — Emphasizes execution model, not I/O concurrency
- **"Long-Running Task Agent"** — Clarifies time scale and autonomy
- **"Supervised Autonomous Agent"** — Explicitly includes human oversight capability
- **"Async Orchestrated Agent"** vs. **"Async Choreographed Agent"** — Specifies coordination pattern

**Standards Opportunity:**

The industry lacks a definition equivalent to REST or ACID properties for agent systems. A minimal spec covering:
- Task invocation semantics (sync/async with respect to human caller)
- Execution model (foreground/background, run-to-done/checkpoint-pause)
- Coordination pattern (orchestration/choreography specifics)
- Observability contract (what humans see in real-time)

...would immediately clarify technology choices and reduce architectural rework.

## Implications

**For Architecture Decisions:**

You cannot evaluate async agent tools without first deciding: What problem are you solving? If it's task parallelization, async concurrency patterns suffice. If it's human productivity through autonomous execution, you need to specify the autonomy boundaries and human control points. These are orthogonal concerns currently lumped under "async."

**For Build vs. Buy:**

Anderson's work demonstrates that custom async agents are now viable for individual developers (feasibility threshold crossed). This shifts competition from "can you build it?" to "can you orchestrate it well?" Enterprise tools win on UX of task submission and result retrieval, not on underlying model capability. If you can articulate your orchestration/choreography model, custom solutions may be cheaper.

**For Multi-Agent Systems (2026 Trend):**

As teams move from single-agent to multi-agent workflows, definitional gaps become blocking. You need explicit answers on how agents coordinate, what "async" means in your choreography, and whether humans remain in the loop. Generic "async agent frameworks" will fail to capture domain-specific coordination needs.

**For Oversight and Safety:**

Conflating "asynchronous execution" with "unsupervised execution" is a safety risk. The best async agent patterns enable human intervention at semantic breakpoints (ambiguity, policy violation, resource threshold). If you can't articulate these breakpoints, your agent orchestration is under-designed.

## Sources

- [Async Coding Agents "From Scratch" — Benjamin Anderson](https://benanderson.work/blog/async-coding-agents/)
- [Synchronous and Asynchronous AI agents: Which one's better for your businesses? — DevRev](https://devrev.ai/blog/case-asynchronous-ai-agents)
- [Why Your AI Agents Need Async — Sweety Tripathi, Coinmonks](https://medium.com/coinmonks/why-your-ai-agents-need-async-7d13a2afe59c)
- [Async Agents: Signal Over Noise — Zed's Blog (Jessie Frazelle)](https://zed.dev/blog/up-your-async-game-jessie-frazelle)
- [Asynchronous AI: The Quiet Revolution Transforming How We Work — Windmill](https://gowindmill.com/resources/guides/agent-driven-software-future-of-saas/)
- [Creating asynchronous AI agents with Amazon Bedrock — AWS ML Blog](https://aws.amazon.com/blogs/machine-learning/creating-asynchronous-ai-agents-with-amazon-bedrock/)
- [Async Agents Documentation — PraisonAI](https://docs.praison.ai/docs/features/async)
