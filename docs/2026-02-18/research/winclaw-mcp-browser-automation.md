# WinClaw: MCP Integration for Browser Automation

| | |
|---|---|
| **Source** | GitHub - itc-ou-shigou/winclaw |
| **URL** | [github.com/itc-ou-shigou/winclaw](https://github.com/itc-ou-shigou/winclaw) |
| **Researched** | 2026-02-18 |

## Overview

WinClaw is a local-first personal AI assistant platform that bridges messaging channels (WhatsApp, Telegram, Slack, Discord, etc.) with native desktop automation through MCP (Model Context Protocol) integration. It runs as a native daemon on Windows, macOS, and Linux, enabling agentic automation of browser and desktop applications while maintaining data sovereignty through local infrastructure.

## Key Points

- **MCP-native architecture**: Treats external tools as first-class MCP servers with stdio and SSE transport support; browser control exposed via `mcp__chrome_devtools_*` namespaced tools
- **Windows-first implementation**: Standalone EXE installer with bundled Node.js 22 LTS eliminates prerequisite friction; auto-detects Chrome debug ports (9222-9229) via PowerShell script
- **Desktop-specific agent capabilities**: Four native Windows skill plugins (system, explorer, office, outlook) provide COM Automation and Registry access for Office applications and email workflows
- **Safety guardrails**: `before_tool_call` hook blocks destructive operations (closing tabs, terminating Chrome) to protect user sessions during autonomous execution
- **Multi-model failover**: Supports Claude Pro/Max via OAuth, OpenAI, and OpenAI-compatible providers with configurable model rotation and concurrency
- **Context efficiency**: Dynamic skill filtering activates for 100+ tool installations; tunable limits prevent token overflow in large agent scenarios

## Technical Details

**Browser Automation Flow**:
```
Agent → mcp__chrome_devtools_* → Chrome DevTools Protocol → noVNC →
websockify → VNC Server → Desktop
```

Chrome runs with remote debugging enabled on dynamically selected ports. VNC-over-WebSocket provides the transport layer to the noVNC client in the agent's execution context.

**Tool Namespacing**: External MCP servers are namespaced as `mcp__<server-name>__<tool-name>`, allowing clean tool discovery and namespace isolation within the agent's tool calling interface.

**Model Recommendations**: Anthropic Opus 4.6 recommended for long-context strength and resistance to prompt injection when delegating to untrusted user requests.

**Gateway**: Listens on `http://127.0.0.1:18789/` with token-based auth; local mode preferred for single-machine deployment to maintain security boundaries.

## Implications

**For practitioners building agents**: WinClaw demonstrates a production-grade pattern for MCP tool integration at scale—tool discovery, transport abstraction (stdio vs. SSE), and reconnection logic provide a reference implementation for systems managing 50+ external tool providers.

**Desktop automation trade-offs**: VNC pipeline trades latency for compatibility across platforms; suitable for human-in-the-loop scenarios but not ideal for sub-100ms response requirements. Pre-execution planning (action sequencing) is critical to reduce round trips.

**When to use**: Ideal for autonomous office automation (Excel/Word/Outlook workflows), cross-platform deployment where you control infrastructure, and scenarios requiring local data residency. Less suitable if you need sub-second click-response feedback or have restrictive network topology (VNC/websockify may conflict with zero-trust security).

**Architecture decision**: By exposing browser control as MCP tools rather than custom APIs, WinClaw achieves extensibility—additional MCP servers (database, APIs, LLMs) integrate uniformly without driver code changes.

## Sources

- [WinClaw on GitHub](https://github.com/itc-ou-shigou/winclaw) - Source repository with full implementation details and installation guides
