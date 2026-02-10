# LLMs as the new high level language

| | |
|---|---|
| **Source** | Federico Pereiro |
| **URL** | [federicopereiro.com/llm-high](https://federicopereiro.com/llm-high/) |
| **Researched** | 2026-02-07 |

## Overview

Federico Pereiro argues that LLM agents represent a fundamental shift in programming paradigm—similar to how high-level languages abstracted assembly. The thesis: multiple autonomous agents driven by documentation and structured context can deliver 10x productivity gains despite generating lower-quality code, because quantity of working functionality at scale outweighs code elegance.

## Key Points

- **Paradigm Shift**: LLMs transition developers from code-writing to system design and agent fleet management. Documentation becomes executable specification, not afterthought.

- **Structural Model**: The architecture uses two persistent stocks (documentation, implementation) and two dynamic flows (agent dialogs, discrete tasks). Tasks nest hierarchically with status tracking to manage complexity.

- **MCP as Integration Fabric**: Model Context Protocol acts as "XMLHTTPRequest for LLMs"—enabling agents to break application silos and dynamically integrate external data/services without monolithic coupling.

- **Quality Trade-off**: Acknowledges LLM code is "much worse" than handwritten code, but argues working at scale beats local optimization. Problem: volume risks incomprehensibility; solution: use "good substrate" (lower-level abstractions) to compress implementation.

## Technical Details

The framework treats frontend as "documentation + agents" and backend as "substrate/stack." Agents operate through markdown dialogs (streamed reasoning) and task decomposition. MCP breaks architectural silos—agents interface with external systems as first-class capabilities rather than embedding knowledge in prompts.

Key insight: code quality degrades with LLM generation, but the cost structure flips—you're now optimizing for agent clarity and system substrate strength, not individual code elegance.

## Implications

**For architects**: This challenges classical code-quality metrics (complexity, maintainability). You must invest heavily in:
- Documentation quality and specificity (becomes your API to agents)
- Substrate design (compression layer between agents and infrastructure)
- Task decomposition and status tracking (replaces code review as control mechanism)

**Decision points**: This model works when agent-generated code volatility is acceptable (search, analysis, rapid iteration) but may not fit safety-critical systems without additional guardrails. The productivity win assumes strong documentation discipline—weak specs negate the agent advantage.

**Architectural shift**: Agents become your compilation layer. Your competitive advantage moves from writing perfect code to designing perfect specifications and substrates.

## Sources

- [federicopereiro.com/llm-high](https://federicopereiro.com/llm-high/) - Original article proposing LLM agents as high-level programming paradigm
