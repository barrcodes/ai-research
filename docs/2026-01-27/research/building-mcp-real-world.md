---
title: Building with MCP, for Real
source: jakeworth.com
url: https://jakeworth.com/posts/building-with-mcp-for-real/
researched_at: 2026-01-27T00:00:00Z
---

# Building with MCP, for Real

## Overview

MCP abstracts away integration complexity but doesn't eliminate it—it redistributes it from transport to semantic understanding. This piece challenges the "magical" narrative by exposing the engineering discipline required: tool definitions must match query sophistication, data quality problems surface rather than hide, and security considerations deserve more industry conversation than they currently receive.

## Key Points

- **No magic: tight coupling persists**—Simple tool definitions handle simple queries; complex autonomous behavior demands correspondingly sophisticated schema and constraint specifications. The abstraction buys you transport elegance, not reasoning capability.

- **Data quality becomes critical**—Unlike traditional APIs where ambiguous data stays hidden in the application layer, MCP exposes interpretation challenges directly to the LLM. "Battle status: 1" requires clear semantics or fails silently in unexpected ways.

- **TypeScript dominates for good reason**—LLMs write and reason about TypeScript naturally, reducing iteration cycles during development. Language choice directly impacts development velocity with agentic systems.

- **Schema-first engineering is non-negotiable**—Real database naming conventions (legacy tables like "TblNinjas") can't be guessed. Providing complete schema context during prompting is essential; partial information creates hallucination opportunities.

- **Agentic patterns should be planned upfront**—The author wasted iteration cycles manually re-explaining previously-generated code. Full agentic loops (LLM generates code → LLM uses it → refinement) save engineering time but require architectural commitment.

## Technical Details

**Development Velocity**: 30 minutes to functional MCP server with documentation suggests lower barrier to entry than traditional API development—but only for the transport layer.

**Implementation Trade-off**: The security-capability tension deserves formal treatment in architecture decisions. Granting LLM autonomy over valuable business data introduces attack surfaces (prompt injection, capability confusion) that differ fundamentally from human-operated APIs.

**Technology Maturity**: Classified as "Assess" on Technology Radar—appropriate for R&D and internal tooling, risky for revenue-critical systems without clear ROI methodology.

## Implications

1. **For Architecture**: MCP succeeds where it simplifies transport, not reasoning. If your problem is "integrate with this database," MCP saves weeks. If it's "autonomous decision-making over sensitive data," MCP surfaces complexity you need to own explicitly.

2. **For Hiring**: Prioritize engineers comfortable reasoning about LLM behavior and data semantics, not just API mechanics. MCP-fluent engineers need both ML intuition and database design discipline.

3. **For Roadmaps**: Plan agentic loops from day one if autonomous operation is the goal. Retrofitting tool definitions for self-iteration costs more than designing for it initially.

## Related

- [Model Context Protocol](https://modelcontextprotocol.io/) - Official MCP specification and documentation
- [Hacker News discussion](https://news.ycombinator.com/item?id=46780397) - Community responses and additional perspectives
