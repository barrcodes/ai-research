# Show HN: Smooth CLI – Token-efficient browser for AI agents

| | |
|---|---|
| **Source** | Smooth Documentation |
| **URL** | [docs.smooth.sh/cli/overview](https://docs.smooth.sh/cli/overview) |
| **Researched** | 2026-02-06 |

## Overview

Smooth CLI is a specialized browser automation platform designed specifically for AI agents that delegates low-level UI interaction to a dedicated model layer. Rather than requiring agents to execute coordinate-based click commands or parse raw DOM elements, agents issue high-level natural language goals while Smooth handles the mechanical browser work. This architectural separation claims 5x token cost reduction and 20x faster execution by offloading UI navigation from large reasoning models.

## Key Points

- **Specialized model layer**: UI interaction delegated to a dedicated model rather than burning tokens on a 1T+ parameter general-purpose LLM doing UI navigation
- **Natural language abstraction**: Agents request outcomes ("search flights NYC to LA") instead of primitives ("click(x=342, y=128)")
- **Cloud-hosted infrastructure**: On-demand parallel browser instances with no local setup required
- **Network routing**: Proxy support to bypass captchas and access location-restricted content
- **Comprehensive automation scope**: Web scraping, form filling, multi-step workflows, authenticated sessions, video recording, JavaScript execution, PDF generation

## Technical Details

**Architecture**: Smooth acts as an abstraction layer between AI agents and browser automation. Agents communicate via REST API with task semantics, while Smooth's specialized model interprets these requests and executes the actual browser interactions through its browser extension system.

**Session management**: Interactive browser profiles support authenticated workflows, allowing agents to maintain state across multiple tasks. File upload/download capabilities enable data pipeline integration.

**Extensibility**: Custom Python functions, browser extensions (.zip deployments), and proxy configuration provide flexibility for complex workflows. Video recording of automated sessions creates audit trails for QA/testing use cases.

**Cost model**: Token efficiency derives from **model specialization** rather than multi-modal scaling. A lightweight model trained for UI interaction consumes fewer tokens than routing vision models and interaction parsing through a general-purpose reasoning model.

## Implications

**When to adopt**: For agentic systems handling deterministic web automation (e-commerce, form processing, data extraction, QA testing). The value proposition peaks when agents previously used vision+reasoning for UI tasks, or when token costs significantly impact economics.

**Architectural trade-off**: Adds operational dependency on Smooth's infrastructure. On-demand scaling and cloud hosting eliminate local DevOps burden but introduce vendor lock-in and latency considerations. Consider for deterministic workflows where latency is acceptable (batch processing) over real-time interactive use cases.

**Competitive positioning**: Positioning against generic browser automation (Selenium, Playwright) + vision models. The innovation is not UI automation itself (well-solved), but **efficient task-to-interaction translation through specialization**.

**Integration readiness**: Native integration paths for Claude Code and other agent frameworks indicate focus on the LLM agent market. Teams already using agent-based architectures have clear on-ramps.

## Sources

- [Smooth CLI Overview](https://docs.smooth.sh/cli/overview) - Core documentation and feature overview
- [Smooth Documentation Index](https://docs.smooth.sh/llms.txt) - Complete API reference and capability matrix
