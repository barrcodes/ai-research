# Coding Agents Have Replaced Every Framework I Used

| | |
|---|---|
| **Source** | Alain Dichiappari's Blog |
| **URL** | [blog.alaindichiappari.dev/p/software-engineering-is-back](https://blog.alaindichiappari.dev/p/software-engineering-is-back) |
| **Researched** | 2026-02-07 |

## Overview

The article argues that AI coding agents have fundamentally shifted how experienced engineers approach architecture. Rather than adopting monolithic frameworks, developers now use agents to generate boilerplate and implementation details while focusing on high-level design decisions. This inverts the traditional framework-driven development model, prioritizing architectural autonomy and minimal tool composition over convention-based abstractions.

## Key Points

- **Boilerplate elimination via agents**: Tasks previously handled by ORMs, build tools, and framework scaffolding (CRUD, database schema generation, monorepo configuration) are now generated on-demand by AI agents
- **Composable tooling over frameworks**: Simple, decades-old tools (Bash, Makefiles) replace modern framework complexity when agents handle implementation details
- **Deferred architecture decisions**: The shift enables solving immediate problems without framework-enforced structures designed for hypothetical future scale scenarios
- **Expertise amplification**: LLM-driven development rewards strong fundamentals—the ability to articulate architecture and constraints becomes more valuable than framework knowledge

## Technical Details

The practical shift manifests in three areas:

1. **Generation-first approach**: Agents handle network configuration, API structure, and CRUD scaffolding—freeing architects to focus on domain logic and constraints
2. **Tool minimalism**: Replaces complex build systems and dependency managers with simpler orchestration (e.g., Makefiles), as agents manage multi-file generation and updates
3. **Vendor lock-in elimination**: Custom solutions built by agents avoid framework ecosystem constraints, enabling genuine architectural flexibility

The model assumes engineers have sufficient expertise to specify constraints and quality standards that agents must satisfy.

## Implications

**For architects**: This reshapes the cost-benefit of framework adoption. Frameworks previously justified by development velocity—they enforce conventions, reduce decision-making—become friction when agents generate quality code quickly. The trade-off inverts: frameworks now add complexity instead of reducing it.

**For hiring and skill development**: Organizations must hire or develop engineers with strong fundamentals (distributed systems, database design, API semantics) rather than framework expertise. Junior engineers may face steeper learning curves without framework-provided guardrails.

**For project structure**: Teams can defer architectural decisions (microservices, caching layers, async patterns) until agents can implement them precisely when needed, rather than upfront. This aligns with YAGNI principles but requires disciplined constraint specification.

**Risk consideration**: Agent-generated code quality depends entirely on prompt clarity and engineer expertise to validate outputs. Unlike frameworks that encode best practices, this model places full responsibility on the architect.

## Sources

- [Alain Dichiappari's Blog](https://blog.alaindichiappari.dev/p/software-engineering-is-back) - Personal perspective on framework vs. agent-driven development patterns
