# Satgate-Proxy: MCP Budget Controls

| | |
|---|---|
| **Source** | GitHub - SatGate-io/satgate-proxy |
| **URL** | [github.com/SatGate-io/satgate-proxy](https://github.com/SatGate-io/satgate-proxy) |
| **Researched** | 2026-02-18 |

## Overview

Satgate-proxy solves a critical cost-control gap in Model Context Protocol deployments by providing transparent budget enforcement at the proxy layer. It intercepts tool calls before execution and rejects those exceeding configurable spending limits, preventing runaway agent costs—a real vulnerability where uncapped tool calls can burn hundreds of dollars in minutes.

## Key Points

- **Dual operational modes**: Local mode (in-process, client-side enforcement) and SaaS mode (server-side via L402 macaroons for enterprise governance)
- **Transparent JSON-RPC proxy** intercepts `tools/call` messages, deducts costs, and rejects overages without modifying client or server code
- **Granular pricing model** supports per-tool costs and wildcard defaults for unlisted tools, enabling fine-grained budget strategies
- **Zero external dependencies**—uses only Node.js built-ins (child_process, http, https, fs), enabling instant `npx` execution with no installation friction
- **Hard spending limits** enforced via CLI: `npx satgate-proxy --local --budget 5.00 --server @modelcontextprotocol/server-google-search`

## Technical Details

Satgate-proxy operates as a JSON-RPC middleware layer. In local mode, it spawns the MCP server as a child process, intercepts each outbound tool call message, deducts the tool's configured cost from a running budget, and immediately rejects calls that would exceed the limit. Configuration uses a simple file for tool pricing definitions.

The architecture is deliberately minimal: no framework dependencies, no external services required for local operation, and a straightforward spawn-and-proxy pattern. This design choice directly enables the zero-friction deployment model—users can run it inline without npm install delays.

## Implications

**For architecture**: This solves a real operational blind spot in agentic systems. MCP's tool-call model creates unbounded cost exposure; Satgate-proxy shifts cost governance from application logic to infrastructure. Organizations deploying agents should treat this as a required guardrail, not optional.

**Decision point**: Local mode suits development and single-user scenarios; SaaS mode becomes mandatory for multi-tenant or team deployments requiring audit trails and centralized policy enforcement.

**Practical trade-offs**: The simplicity of local mode (one CLI invocation) comes at the cost of distributed enforcement—each client maintains its own budget. Teams will need to choose between convenience and centralized control.

**When to use**: Essential whenever agents have access to cost-bearing tools (search, APIs, compute). Particularly valuable for exploration phases and untested agent behaviors where cost bounds aren't yet understood.

## Sources

- [SatGate-io/satgate-proxy](https://github.com/SatGate-io/satgate-proxy) - Official repository with implementation details and usage examples
