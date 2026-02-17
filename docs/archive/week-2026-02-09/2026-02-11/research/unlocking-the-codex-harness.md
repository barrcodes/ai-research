# Unlocking the Codex harness: how we built the App Server

| | |
|---|---|
| **Source** | OpenAI Engineering Blog |
| **URL** | [openai.com/index/unlocking-the-codex-harness/](https://openai.com/index/unlocking-the-codex-harness/) |
| **Researched** | 2026-02-11 |

## Overview

OpenAI's Codex App Server is a bidirectional JSON-RPC protocol that abstracts the Codex agent harness—the core loop orchestrating agent-model-tool interactions—into a stable, platform-agnostic interface. The server enables consistent agent behavior across web, desktop, IDE, and CLI surfaces while maintaining backward compatibility and supporting rich streaming interactions (diffs, approvals, incremental reasoning).

## Key Points

- **Unified harness abstraction**: Codex App Server exposes the core agent loop, thread lifecycle, configuration, and tool execution through a single JSON-RPC surface, eliminating surface-specific reimplementations and reducing maintenance burden.

- **Three-tier event model**: Primitives (Item, Turn, Thread) provide structured semantics for complex agent interactions—Items lifecycle (started/delta/completed) enables progressive rendering, Turns isolate agent work units, Threads persist state across reconnects.

- **Bidirectional protocol design**: Server initiates requests (approval flows, agent reasoning pauses), not just responses—the client must handle both reactive notifications and server-initiated requests, enabling client-side policy enforcement and real-time UI feedback without long polling.

- **Transport agnostic**: JSON-RPC over stdio + JSONL framing works across local processes (VS Code, Desktop), containerized services (Codex Web), and remote scenarios (planned TUI refactor to enable headless agents with local control surface).

- **Backward compatibility as design constraint**: New servers support old clients through stable protocols; new features advertised in initialize handshake with feature flags; allows decoupled release cycles (Xcode updates independently of Codex binary).

## Technical Details

### Architecture Layers

1. **Codex Core** (Rust library): Agent loop, thread persistence, tool execution, MCP integrations, plugin system
2. **App Server process**: Stdio reader + JSON-RPC dispatcher + thread manager + core sessions
3. **Client bindings**: Language-specific (Go, Python, TypeScript, Swift, Kotlin) implementations of JSON-RPC client

### Protocol Primitives

```
Item (atomic unit):
  - /started: begin rendering
  - /delta: stream chunks (for streaming types)
  - /completed: finalize with payload

Turn (agent work unit):
  - Begins on client input submission
  - Contains sequence of Items (user msg, tool calls, diffs, agent response)
  - Ends when agent finishes all outputs

Thread (durable session):
  - Multiple turns per thread
  - Persistent history enables reconnection/catch-up
  - Can be resumed, forked, archived
```

### Integration Patterns

- **Local apps/IDEs**: Bundle platform binary, long-lived stdio child process, pinned versions
- **Web**: Container-based execution with stdio-over-network tunneling, stateless browser UI, server-side persistence
- **TUI (refactoring)**: Moving from in-process Rust API to JSON-RPC client, enables remote agent execution with local CLI control

### Approval Flow Example
Server sends `item/commandExecution/requestApproval` with reason ("run pnpm test"), pauses turn, waits for client `allow`/`deny` response—decentralizes policy to client while maintaining sequential turn semantics.

## Implications

1. **Agentic platform pattern**: The three-tier model (Items/Turns/Threads) and bidirectional protocol define a reusable contract for agent systems—applicable beyond Codex to other agentic frameworks. Feature flags in initialize handshake provide evolution path without breaking clients.

2. **Product velocity tradeoff**: Early iteration as native surfaces (TUI) vs. protocol complexity. Moving TUI to JSON-RPC is a refactor, not new surface—demonstrates willingness to accept integration overhead for consistency (forces evaluation of which surfaces truly need special-casing).

3. **Decoupling release cycles**: Backward-compatible JSON-RPC allows IDE integrations (Xcode, JetBrains) to adopt new agent capabilities without shipping client updates—critical for ecosystems with long validation cycles. Initialize handshake enables graceful degradation.

4. **Client policy enforcement**: Approval requests reverse the typical request/response flow—client is not passive JSON consumer but an active participant in execution policy. This distributes trust/safety decisions and unlocks audit logging on client side.

5. **Streaming and state reconstruction**: Rich event stream (delta events) + thread persistence enable clients to drop and reconnect, catch up on missed events—required for web sessions but generalizes to unreliable networks and client crashes.

6. **Language-agnostic integration**: JSON-RPC + schema generation (TypeScript via Rust reflection, JSON Schema for other codegen tools) reduces friction vs. maintaining native bindings per language. But codegen is only scaffolding—business logic still requires custom client logic per platform.

## Trade-offs & Design Decisions

- **JSON-RPC "lite" over strict JSON-RPC 2.0**: Drops jsonrpc header, frames as JSONL—simpler for stdio but nonstandard, reduces ecosystem tooling reuse.

- **Full harness vs. MCP subset**: App Server provides rich session semantics (diffs, forks, approvals); MCP server mode is lightweight alternative but loses provider-specific interactions. Design bifurcates users based on feature requirements.

- **Stateful server vs. stateless service**: App Server is long-lived per thread, maintains execution context—simpler semantics than lambda-style request handlers, but requires session management and recovery logic on client reconnects.

- **Stdio transport for web**: Container-based Codex Web tunnels stdio over network (WebSocket-like)—works but adds complexity. Could have chosen HTTP streaming or WebSocket natively but chose to reuse local transport protocol.

## Implementation Insights

- Initialize handshake exchanges clientInfo/userAgent for capability negotiation—permits seamless protocol evolution with feature detection.
- Thread manager spins up one Rust core session per thread—allows parallel work (Desktop app orchestrating many agents) without shared mutable state.
- Event transformation layer (stdio reader + processor) translates low-level Rust events to UI-ready JSON-RPC notifications—prevents protocol churn when Codex core refactors.

## Sources

- [Codex App Server README](https://github.com/openai/codex/blob/main/codex-rs/app-server/README.md) - Source repository
- [Unrolling the Codex agent loop](https://openai.com/index/unrolling-the-codex-agent-loop/) - Companion article on core agent loop
- [Codex CLI Repository](https://github.com/openai/codex) - Full source code
- [OpenAI Agents SDK](https://openai.github.io/openai-agents-js/) - MCP-based agent framework
