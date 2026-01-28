---
title: Sandbox Agent SDK - Unified API for Coding Agents
source: GitHub (rivet-dev/sandbox-agent)
url: https://github.com/rivet-dev/sandbox-agent
researched_at: 2026-01-28T00:00:00Z
---

# Sandbox Agent SDK: Unified API for Coding Agents

## Overview

Sandbox Agent SDK abstracts away agent fragmentation by providing a single standardized API across four major coding agents (Claude Code, Codex, OpenCode, Amp). It operates as an adapter layer that normalizes agent-specific protocols into a universal event-driven interface, enabling agent switching without client code changes. The system supports both embedded mode (local subprocess) and server mode (HTTP API), with a lightweight Rust binary for portable deployment.

## Key Points

- **Universal Adapter Pattern**: Dedicated adapters per agent (e.g., `claude_adapter.rs`) translate between normalized API and agent-specific protocols—eliminating vendor lock-in at the integration layer
- **Dual Deployment**: Embedded mode for local development, server mode for sandbox integration; both accessed via identical TypeScript SDK
- **Event-Driven Architecture**: Sessions stream normalized JSON events (messages, tool calls, errors, results) via Server-Sent Events—producers handle external persistence
- **Asymmetric Feature Coverage**: Codex leads with MCP tools and command execution; Claude Code lacks tool calls; OpenCode focuses on human-in-the-loop workflows
- **Lightweight Installation**: Single-command deployment via shell script; agents auto-install on-demand via binary distribution

## Technical Details

**Architecture**:
```
Client SDK/CLI/REST → Universal API Layer → Agent-Specific Adapters → Agents
                                 ↓
                        Normalized JSON Events
                                 ↓
                     External Persistence (caller responsibility)
```

**Supported Agents** (with limitations):
| Agent | Stable | Text | Tool Calls | Results | MCP Support |
|-------|--------|------|-----------|---------|-------------|
| Claude Code | Yes | ✓ | ✗ | ✗ | - |
| Codex | Yes | ✓ | ✓ | ✓ | ✓ |
| OpenCode | No | ✓ | - | - | - |
| Amp | No | ✓ | - | - | - |

**Deployment** (Server):
```bash
curl -fsSL https://releases.rivet.dev/sandbox-agent/latest/install.sh | sh
sandbox-agent server --token "$TOKEN" --host 127.0.0.1 --port 2468
```

**Basic Usage** (TypeScript):
```typescript
const client = await SandboxAgent.start(); // or .connect() for remote
await client.createSession("demo", { agent: "codex", agentMode: "default" });
await client.postMessage("demo", { message: "Hello" });
for await (const event of client.streamEvents("demo", { offset: 0 })) {
  console.log(event.type, event.data); // Normalized events
}
```

## Implications

**When to Adopt**:
- Building agent-agnostic platforms (trading agent coverage for flexibility)
- Requiring centralized transcript audit trails (you own persistence strategy)
- Hedging against single-agent deprecation or API churn
- Integrating agents into sandboxed/containerized environments

**Critical Trade-offs**:
- **Session State**: No built-in persistence—you must handle external storage (database, cache, logs). This is intentional design, not a limitation
- **Feature Variance**: Different agents have different capabilities. The SDK normalizes schema but not semantics—test thoroughly across agents
- **Maturity**: Three of four supported agents are experimental; production deployments should standardize on Codex or Claude Code (but then lose the abstraction benefit)

**Architectural Decisions**:
- Deliberately out-of-scope: disk persistence, LLM wrappers, git management, sandbox provider abstraction. Consumer owns these layers
- This minimalism keeps the SDK lightweight and focused on the agent protocol translation problem

**Alternative Considerations**:
- If you only use one agent (e.g., Claude Code exclusively), the abstraction overhead isn't justified
- For LLM-agnostic agent building, consider Vercel AI SDK (different problem domain)
- For agent management at scale, this pairs well with your own session store + transcript normalization layer

## Related

- [Vercel AI SDK](https://sdk.vercel.ai) - LLM-agnostic SDK (different scope: LLM providers vs. coding agents)
- [Rivet Platform](https://rivet.dev) - Parent project for workflow orchestration
- Claude Code Documentation - Primary supported agent implementation reference
