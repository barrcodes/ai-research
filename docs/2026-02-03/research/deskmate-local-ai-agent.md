# Deskmate: A Local-First AI Agent for Executing Real System Actions

| | |
|---|---|
| **Source** | GitHub Repository |
| **URL** | [github.com/sarkar-ai-taken/deskmate](https://github.com/sarkar-ai-taken/deskmate) |
| **Researched** | 2026-02-03 |

## Overview

Deskmate is an open-source execution agent that bridges natural language commands to local system actions via messaging platforms. Unlike sandboxed AI tool environments, it grants unrestricted local access through a three-layer architecture (agent, gateway, client), enabling real-time system control from Telegram or future chat platforms without inbound port exposure.

## Key Points

- **Three-layer architecture** separates concerns: Claude SDK agent layer with full local tool access, authentication/approval gateway, and platform-specific chat clients (Telegram via grammY). Extensible for Discord, Slack without core modifications.

- **Approve-by-default security model** auto-approves reads; sensitive writes to protected folders (Desktop, Documents, Downloads) require inline button confirmation via messaging interface. User allowlisting by platform and ID.

- **Real system capabilities**: Bash execution (2-min timeout, 10MB buffer), file operations, screenshot capture, git commands, AppleScript application control on macOS, process monitoring. No sandboxing constraints.

- **Service integration** runs as systemd (Linux) or launchd (macOS) daemon with boot-time startup. Session continuity maintains conversation memory; 30-minute idle timeout for isolation.

- **Dual deployment modes**: Gateway mode for messaging clients, MCP server mode for Claude Desktop integration, or both simultaneously. No exposed inbound ports—clients initiate outbound connections.

## Technical Details

The agent layer leverages Claude Code SDK's native tool support (Bash, Read, Write, Edit, Glob, Grep) rather than wrapping third-party LLM APIs. Gateway abstracts authentication and session routing from client implementations using a plugin interface.

Security relies on input validation via Zod schemas, structured audit logging, and approval gates rather than capability restrictions. Protected folder writes check against a predefined list; everything else executes directly. Session isolation prevents cross-user interference.

The 2-minute bash timeout and 10MB output buffer prevent resource exhaustion from runaway commands. Screenshot capture integrates with system APIs (Windows/macOS/Linux variants).

## Implications

**For platform builders**: Deskmate demonstrates that local-first agent execution is viable without sandboxing when paired with explicit approval workflows. The three-layer design decouples agent logic from transport, enabling rapid chat platform adoption. This pattern suits internal tools, personal automation, and enterprise environments where users control the deployment.

**Decision points**: Choose Deskmate if you need unrestricted local access with audit trails. Avoid if you require strict capability isolation or multi-tenant sandboxing. The approve-by-default model trades autonomy for transparency—users see every action before write operations complete.

**Architectural trade-off**: Running as a service daemon trades convenience (automatic startup) against operational complexity. Service management becomes part of the deployment story.

## Sources

- [sarkar-ai-taken/deskmate](https://github.com/sarkar-ai-taken/deskmate) - Open-source local execution agent with messaging platform integration
