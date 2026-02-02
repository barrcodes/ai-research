# The Two Agentic Loops: How to Design and Scale Agentic Apps

| | |
|---|---|
| **Source** | Plano AI |
| **URL** | [planoai.dev/blog/the-two-agentic-loops-how-to-design-and-scale-agentic-apps](https://planoai.dev/blog/the-two-agentic-loops-how-to-design-and-scale-agentic-apps) |
| **Researched** | 2026-02-02 |

## Overview

The two-loop framework separates agentic systems into an **inner loop** (agent reasoning and tool execution) and an **outer loop** (orchestration, governance, and resource control). This architectural pattern addresses a fundamental constraint: agents optimize for goal completion through side effects, so safety constraints must live in external infrastructure rather than relying on agent self-enforcement.

## Key Points

- **Inner Loop (Intelligence):** Agent iterates through observe → reason → act → evaluate until goal completion. Stays narrowly focused on domain-specific logic and tool use.

- **Outer Loop (Delivery):** Handles cross-cutting concerns—routing, cost limits, rate limiting, budget enforcement, observability, provider abstraction—preventing the inner loop from working against governance constraints.

- **Separation of Concerns:** Safety constraints cannot be reliably delegated to goal-directed agents. Infrastructure must enforce boundaries that don't compromise the agent's ability to pursue objectives.

- **Graceful Degradation:** When limits are hit (cost, tokens, rate), the outer loop manages checkpoints, fallbacks, and summarization rather than hard failures.

- **Centralized Configuration:** Declarative YAML-based definitions for agent routing, filtering, and model selection eliminate repeated plumbing code across teams.

## Technical Details

The pattern prevents the "hidden middleware tax" where teams rebuild the same routing logic, instrumentation, and provider abstractions repeatedly. By centralizing these concerns in the outer loop, organizations ensure consistent governance regardless of whether agents are built with LangChain, raw Python, or other frameworks.

The architecture treats governance as a separate system layer: budget limits work because they're enforced before execution, not through agent restraint. Rate limiting, moderation, and resource allocation all sit outside the agent's control loop, creating a clear failure domain.

## Implications

This framework addresses a critical scaling problem: as agentic systems proliferate across teams, each team shouldn't reinvent provider abstraction, monitoring, or safety controls. Organizations adopting this pattern should:

1. **Consolidate infrastructure**: Build the outer loop as a centralized platform service, not library code in each agent implementation
2. **Define clear boundaries**: Keep agent code focused solely on business logic; route all cross-cutting concerns through the outer loop
3. **Plan for graceful limits**: Design fallback strategies for cost overruns and rate limits upfront, not as afterthoughts
4. **Use declarative config**: Drive routing, model selection, and filter chains through configuration, not code changes

The architectural implication is significant: agentic systems at scale are not primarily intelligence problems—they're infrastructure and governance problems.

## Sources

- [Plano AI - The Two Agentic Loops: How to Design and Scale Agentic Apps](https://planoai.dev/blog/the-two-agentic-loops-how-to-design-and-scale-agentic-apps) - Framework separating agent reasoning (inner loop) from orchestration and governance (outer loop)
