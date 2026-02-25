# Claude Code Remote Control

| | |
|---|---|
| **Source** | Anthropic Claude Code Documentation |
| **URL** | [code.claude.com/docs/en/remote-control](https://code.claude.com/docs/en/remote-control) |
| **Researched** | 2026-02-25 |

## Overview

Remote Control bridges local Claude Code sessions with web/mobile interfaces while maintaining all computation and file system access on the developer's machine. Unlike cloud-based Claude Code, this preserves local MCP servers, filesystem context, and project configuration while enabling session continuity across devices—effectively creating a persistent, location-agnostic development environment.

## Key Points

- **Local-first architecture**: All execution remains on the developer's machine; Remote Control is purely a remoting protocol layer over Anthropic's API
- **Multi-device session persistence**: Single conversation context synchronizes across terminal, browser (claude.ai/code), and mobile app—messages from any surface update all others
- **Automatic reconnection**: Sessions survive network interruptions and machine sleep, restoring connection state without manual intervention
- **MCP and tool preservation**: Full access to local Model Context Protocol servers, custom tools, and project configuration from any connected device
- **Security model**: Outbound-only connections via TLS through Anthropic API; no inbound ports; credentials are short-lived and purpose-scoped

## Technical Details

Remote Control operates as a local daemon process (`claude remote-control`) that polls Anthropic's API for incoming messages. Session state flows bidirectionally:

- **Outbound**: Claude Code instance registers with API, polls for remote commands
- **Inbound**: Web/mobile clients send messages to API; server routes to local session via streaming connection
- **Reconnection logic**: Survives ~10 minute network outages; terminal closure or process termination requires restart

Key constraints:
- One remote session per Claude Code instance (can run multiple instances for parallel work)
- Terminal must remain open; no background daemon support
- Subscription requirement: Pro/Max plans only (not Team/Enterprise in current preview)

## Implications

**When to use**: Remote Control targets the workflow interruption problem—start work at desk, continue on phone/tablet/secondary machine without losing conversation context or filesystem access. Ideal for async, context-rich development tasks requiring persistent local tools.

**Trade-offs vs cloud Claude Code on the web**:
- Remote Control: local compute, MCP access, session continuity, terminal-dependent
- Cloud Claude Code: no local setup, parallel sessions, server reliability, cloud-only filesystem

**Architectural significance**: This pattern solves a critical gap in agentic AI tools—preserving execution environment context across interfaces. For teams building AI-assisted workflows, Remote Control demonstrates that cloud-first doesn't require cloud-only execution. The polling model (rather than inbound connections) is notably practical for enterprise firewalls.

**Implementation considerations**: Developers should expect this feature to remain preview-tier for some time. Blocking on terminal availability may not suit production automation; cloud Claude Code remains appropriate for CI/CD or unattended tasks.

## Sources

- [Claude Code Remote Control Documentation](https://code.claude.com/docs/en/remote-control) - Official Anthropic documentation with setup, security model, and limitation details
- [claude.ai/code](https://code.claude.com/) - Claude Code web interface
- [Claude Mobile App](https://apps.apple.com/us/app/claude-by-anthropic/id6473753684) - iOS app supporting Remote Control
