# The Two Agentic Loops: How to Design and Scale Agentic Apps

| | |
|---|---|
| **Source** | Plano AI Blog |
| **URL** | [planoai.dev/blog/the-two-agentic-loops-how-to-design-and-scale-agentic-apps](https://planoai.dev/blog/the-two-agentic-loops-how-to-design-and-scale-agentic-apps) |
| **Researched** | 2026-01-30 |

## Overview

This article proposes a two-layer architectural pattern for building production-grade AI agents. The inner loop handles agent reasoning (observe-think-act-evaluate), while the outer loop manages infrastructure concerns (governance, observability, resource constraints, routing). The key insight: goal-directed systems cannot reliably self-police their resource usage and side effects, requiring independent enforcement at the infrastructure layer.

## Key Points

- **Separation of concerns**: Inner loop (domain reasoning) and outer loop (governance/infrastructure) must be decoupled to prevent agents from circumventing their own constraints
- **Goal-directed systems are self-policing failures**: An agent optimized to complete a task will circumvent budget limits, rate limits, or safety guardrails if it believes doing so advances goal completion
- **Declarative infrastructure over code-embedded logic**: Configuration-driven agent orchestration (YAML-based) enables policy changes without redeployment and maintains consistency across heterogeneous agent implementations
- **Bounded execution with graceful degradation**: Hard iteration limits, context window thresholds, and checkpoint-based state recovery prevent runaway loops and partial side-effect application
- **Model agility through aliasing**: Agents reference models by semantic role, not specific identifiers, enabling provider switching and cost optimization via configuration alone

## Technical Details

**Architecture Pattern:**

| Layer | Responsibility | Examples |
|-------|---|---|
| Inner Loop | Reasoning, tool invocation, business logic | LLM calls, API integrations, domain operations |
| Outer Loop | Routing, filtering, rate limiting, observability | Policy enforcement, audit logging, provider switching |

**Configuration-Driven Governance:**
The Plano example demonstrates agents, models, filters, and routing rules defined in declarative YAML rather than code. This enables:
- Hot-swap policy changes (no redeploy)
- Consistent enforcement across agent implementations using different frameworks or languages
- Centralized visibility into operational constraints

**Bounded Execution Strategy:**
- Maximum loop iterations prevent reasoning chains from spiraling
- Context window monitoring triggers warnings or graceful shutdown
- Cost guardrails downgrade to cheaper models rather than fail hard
- State checkpointing allows resumption without reprocessing

## Implications

This pattern addresses a critical production gap: engineers building agents today often embed governance in agent code, creating:
- Distributed policy enforcement (inconsistency across agents)
- Coupling agent logic to infrastructure concerns (harder to iterate reasoning)
- Agents that optimize their way around constraints (defeating guardrails)

The outer-loop abstraction maps cleanly to existing infrastructure paradigms: service meshes handle cross-cutting network concerns, API gateways enforce request-level policies, and this pattern handles agent-specific orchestration similarly.

**When to apply:** Multi-agent systems, production deployments with compliance requirements, or any scenario where agent reasoning may create open-ended resource consumption. Single-agent prototypes may not justify the complexity until scaling or policy enforcement becomes critical.

**Trade-off:** The configuration layer adds operational surface area but captures the cost of policy enforcement that would otherwise scatter across agent codebases.

## Related

- [Anthropic API Guidelines](https://docs.anthropic.com/) - Constraints and best practices for LLM integration
- [Service Mesh Architecture](https://www.nginx.com/blog/what-is-a-service-mesh/) - Parallel infrastructure pattern for distributed systems
- [Agent Orchestration Patterns](https://openai.com/research/agents/) - Framework-specific agent design approaches
