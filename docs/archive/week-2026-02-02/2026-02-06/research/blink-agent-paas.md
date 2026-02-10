# Blink: Self-Hosted, Open-Source PaaS for AI Agents

| | |
|---|---|
| **Source** | GitHub (Coder) |
| **URL** | [github.com/coder/blink](https://github.com/coder/blink) |
| **Researched** | 2026-02-06 |

## Overview

Blink is a self-hosted platform that abstracts away infrastructure complexity for deploying agentic systems. It decouples agent logic (HTTP handlers) from operational concerns—routing, persistence, multi-channel integration—enabling teams to focus on defining agent behavior rather than orchestration. The key architectural insight: agents are stateless event processors; the platform owns state and channel routing.

## Key Points

- **Agents-as-HTTP-services model**: Agents define event handlers (`on("chat")`) and expose a simple HTTP interface. The Blink server manages deployment, scaling, and routing across Slack, GitHub, and web UI.

- **LLM-agnostic**: Supports Anthropic Claude, AWS Bedrock, Google Vertex, and self-hosted models through Vercel AI SDK integration, avoiding vendor lock-in.

- **Centralized conversation history**: Unlike distributed Claude sessions, all interactions persist in a single database, enabling audit trails, context across channels, and permission enforcement at one point.

- **Built-in tool-calling loops**: Server automatically handles LLM tool-invocation cycles, reducing agent boilerplate compared to raw API usage.

- **Open-source with clear licensing**: Server uses AGPLv3; SDKs use MIT. Full source transparency for compliance-sensitive deployments.

## Technical Details

**Agent structure** leverages TypeScript with event-driven patterns. The Vercel AI SDK abstracts LLM providers, so agents call `streamText()` and `convertToModelMessages()` without provider-specific code. Docker containerization isolates each agent; Blink Server orchestrates deployment and routes events.

**Stack**: Node.js 22+ or Bun, TypeScript, self-hosted infrastructure. No vendor-managed compute layer—you control hardware and data residency.

**Architecture advantage**: By making agents stateless HTTP servers, Blink sidesteps distributed consensus problems. Conversation state, permissions, and routing live server-side, simplifying debugging and access control at scale.

## Implications

**When to adopt**: Organizations with data sovereignty requirements, multi-team governance models, or existing Slack/GitHub automation investment. Blink reduces operational overhead compared to manual orchestration of LangChain or LangGraph agents.

**Trade-offs**: Requires self-hosting operational burden (infrastructure provisioning, monitoring, updates). Early-access status means API stability not yet guaranteed. Less mature ecosystem than established agent frameworks like LangGraph.

**Architectural decision point**: If your deployment model is "manage our own infrastructure and control all data," Blink is worth evaluating. If you prioritize minimal ops overhead, evaluate hosted alternatives or simpler serverless patterns. Current use cases (customer support, CI/CD diagnostics, BI) suggest suitability for internal tools rather than customer-facing agent products.

## Sources

- [Coder/Blink GitHub Repository](https://github.com/coder/blink) - Official source, architecture documentation, and implementation examples
