# ClaudeDesk v3.0 - Agentic GUI for Claude Code with Multi-Phase Workflow

| | |
|---|---|
| **Source** | Reddit r/ClaudeAI |
| **URL** | [old.reddit.com/r/ClaudeAI/comments/1qps6ub](https://old.reddit.com/r/ClaudeAI/comments/1qps6ub/claudedesk_v30_major_redesign_of_the_opensource/) |
| **Researched** | 2026-01-30 |

## Overview

ClaudeDesk is an open-source progressive web application (PWA) that serves as a companion interface for the Claude Code CLI. Version 3.0 introduces a major architectural redesign centered on a structured three-phase workflow (Prompt > Review > Ship) and implements agent orchestration patterns through its MissionControl component. This release demonstrates practical patterns for managing agent state, phase-based execution, and remote agent access via tunnels.

## Key Points

- **MissionControl Phase System** - Three distinct phases (Prompt, Review, Ship) enforce structured workflows; PhaseNavigator enables keyboard shortcuts (1, 2, 3) for rapid phase switching
- **Architecture Overhaul** - Removed 3,381 lines of legacy code, 6 unused hooks, and 3 unused utilities while preserving core capabilities
- **Persistent Session Management** - Sessions survive terminal closure and can be resumed; each session uses git worktree isolation for safe experimentation
- **Visual Tool Timeline** - Graphical representation of all Read/Edit/Bash actions provides auditability and debugging capability
- **File Approval Workflow** - User-mediated file changes prevent unwanted modifications; integrates with git for safety
- **Remote Access Tunnels** - Enable Claude Code sessions to be accessed and controlled from external interfaces
- **MCP Integration** - Model Context Protocol support for extensibility

## Technical Details

### Architectural Patterns

**Phase-Based Execution Model**: The three-phase system (Prompt > Review > Ship) implements a common agent orchestration pattern where:
1. Prompt phase captures user intent and context
2. Review phase presents Claude's proposed changes for approval
3. Ship phase executes and commits changes

This prevents the "chaotic freelancer" problem where agents execute without validation.

**Session Persistence Mechanism**:
- Terminal sessions are detached and stored server-side
- WebSocket connections maintain real-time updates
- Git worktree isolation ensures each session has its own branch, preventing interference
- Message queue system handles async communication between agent and UI

**Web Interface Stack**:
- PWA architecture allows offline capability
- Real-time markdown rendering of Claude's responses
- Keyboard-driven navigation for power users
- Settings drawer (inline) vs modal popups reduces friction

### Implementation Strategy

The redesign focused on "structured workflow" rather than free-form interaction:
- **OnboardingFlow** guides first-time users through setup
- **RepoDock** enables quick repository switching without interrupting workflow
- **SettingsDrawer** keeps configuration accessible but not intrusive
- Complete documentation for Agent API, Tunnel API, and architecture patterns

### Technology Choices

- **Node-pty** for terminal emulation
- **WebSocket streaming** for real-time data
- **AES-256-GCM + Diffie-Hellman** encryption for remote access
- **Git integration** for version control and branch isolation

## Implications

### For Practitioners

1. **Agent State Management** - The session persistence pattern demonstrates how to maintain agent context across terminal/UI boundaries. This is critical for long-running tasks where users cannot stay at their desk.

2. **Workflow Validation Pattern** - The three-phase system with file approval is highly relevant for production use. It mitigates the risk of agents making unvetted changes to critical code.

3. **UI/Agent Decoupling** - Separating the agent (CLI) from the interface (web UI) via WebSocket enables multiple interfaces (mobile, desktop, remote) without modifying the core agent logic.

4. **Keyboard-First Design** - The phase navigator shortcuts (1, 2, 3) and keyboard-driven navigation optimize for developer ergonomics, reducing friction in agent interaction.

5. **Visibility and Debugging** - The visual tool timeline creates an audit log. This is essential for understanding agent decisions in complex multi-step workflows.

### Agent Chaining Patterns

While ClaudeDesk itself is a single-agent interface, its architecture enables agent chaining through:
- **Phase-based handoff** - Output of Review phase feeds into Ship phase
- **Message queue** - Async message processing enables multi-agent coordination
- **Tunnel API** - Remote access allows multiple agents/tools to interact with the same session
- **MCP integration** - Protocol for composing multiple specialized tools/agents

The file approval workflow is particularly relevant: it creates a natural boundary where one agent (Claude Code) can propose changes and another agent (user or validation service) can approve/reject before execution.

## Related

- [ClaudeDesk GitHub Repository](https://github.com/carloluisito/claudedesk) - Source code and architecture documentation
- [Claude Code CLI Documentation](https://github.com/anthropics/claude-code) - Underlying agent interface
- [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) - Extensibility framework used by ClaudeDesk
- [Terminal Session Architecture Patterns](https://en.wikipedia.org/wiki/Pseudoterminal) - Technical foundation for PTY-based agent orchestration
