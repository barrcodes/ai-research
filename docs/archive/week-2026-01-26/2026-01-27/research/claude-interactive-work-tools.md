# Interactive Work Tools Now Available in Claude

| | |
|---|---|
| **Source** | Anthropic, Reddit r/ClaudeAI |
| **URL** | [old.reddit.com/r/ClaudeAI/hot](https://old.reddit.com/r/ClaudeAI/hot/) |
| **Researched** | 2026-01-27 |

## Overview

Anthropic has released interactive tool support in Claude, powered by an extension to the Model Context Protocol (MCP) called MCP Apps. This allows users to interact directly with external applications—Slack, Figma, Asana, Canva, Amplitude, Box, Clay, Hex, and others—without context-switching. Tools now render inline within the chat interface with full interactivity, marking a significant shift in how Claude integrates with enterprise workflows.

## Key Points

- **MCP Apps Extension**: A new framework built on the Model Context Protocol enables any MCP server to expose interactive UI components that render directly in Claude's interface.
- **Zero Context Switching**: Users can draft Slack messages, create Asana tasks, design in Figma, and visualize data all while maintaining conversation continuity with Claude.
- **Broad Platform Support**: Available on Claude web and desktop for Pro, Max, Team, and Enterprise plans; coming to Claude Cowork.
- **Sandbox Security Model**: UIs render in restricted iframes with no access to parent page, cookies, or the host environment, enabling safe third-party integrations.
- **Production-Ready**: MCP Apps is already an official MCP extension with working examples (3D visualization, maps, PDFs, dashboards, music notation).

## Technical Details

### Architecture

The implementation leverages **nested iframe sandboxing**:
1. Host initializes a first sandboxed iframe
2. That iframe launches a second iframe containing the MCP server's UI resources
3. All communication uses standard `postMessage` with JSON-RPC protocol
4. No vendor lock-in; UIs can be built with any framework

### Security Design

- **Iframe isolation**: Restricted permissions, no access to parent DOM or cookies
- **Pre-declared templates**: Hosts review HTML content before rendering
- **Auditable messaging**: All UI-to-host communication visible as JSON-RPC logs
- **User consent**: Tool calls initiated from UI require explicit user approval

### Supported Integrations

**Direct Interactive Tools**:
- **Asana**: Turn prompts into projects, tasks, and timelines visible in Asana
- **Slack**: Search conversations, draft messages with preview, format, and send with review step
- **Figma**: Generate flowcharts, Gantt charts, and diagrams in FigJam from text/images
- **Canvas Apps**: Canva, Hex (analytics), Clay (data enrichment), Amplitude (analytics)
- **File Storage**: Box integration for document access

### Developer Stack

Example implementations available:
- `threejs-server` — 3D visualization
- `map-server` — Interactive geographic data
- `pdf-server` — Document viewer
- `system-monitor-server` — Real-time dashboards
- `sheet-music-server` — Music notation rendering

## Implications

### For Practitioners

**Workflow Consolidation**: Teams can now operate directly from Claude without breaking flow. A security operations team can draft incident messages in Slack, update timelines in Asana, and generate visual diagrams—all in a single unified interface. This reduces cognitive load and eliminates tab-switching overhead in high-pressure scenarios.

**New Integration Vectors**: Organizations building on top of Claude can now expose their tools as MCP servers with interactive UIs. This flips the integration model: instead of Claude calling external APIs and displaying JSON responses, users interact with rich interfaces natively.

**Sandbox-First Design Pattern**: The double-iframe sandboxing with JSON-RPC communication provides a replicable pattern for safely embedding untrusted UIs in LLM products. This matters architecturally because it decouples UI security from API security.

**Cross-Platform Standardization**: MCP Apps is already supported (or coming soon) in Goose, VS Code, and ChatGPT. Developers building MCP servers can now expect interactive UI to work across multiple AI platforms, creating an incentive for a unified ecosystem rather than bespoke integrations.

### For Architecture Decisions

**Separation of Concerns**: The MCP Apps pattern cleanly separates:
- **Data access** (MCP server → external service)
- **UI rendering** (MCP server → sandboxed iframe)
- **User interaction** (iframe ↔ host via postMessage)

This makes it possible to build rich experiences without embedding business logic in the host (Claude).

**Extensibility at the Edge**: Rather than Anthropic shipping Slack/Figma/Asana integrations themselves, the pattern enables third-party tool owners to ship MCP servers. This scales integration development horizontally.

**Minimal Protocol Overhead**: Reusing JSON-RPC over `postMessage` means no new serialization layers or protocol parsing. The surface area for bugs is minimized.

## Related

- [Anthropic brings interactive workplace apps to Claude](https://www.techbuzz.ai/articles/anthropic-brings-interactive-workplace-apps-to-claude)
- [Anthropic launches interactive Claude apps on TechCrunch](https://techcrunch.com/2026/01/26/anthropic-launches-interactive-claude-apps-including-slack-and-other-workplace-tools/)
- [Anthropic extends MCP with a UI framework (TheNewStack)](https://thenewstack.io/anthropic-extends-mcp-with-an-app-framework/)
- [Claude expands tool connections using MCP (Help Net Security)](https://www.helpnetsecurity.com/2026/01/27/anthropic-claude-mcp-integration/)
- [MCP Apps Blog Post](http://blog.modelcontextprotocol.io/posts/2026-01-26-mcp-apps/)
- [MCP Apps Documentation](https://modelcontextprotocol.io/docs/extensions/apps)
- [Interactive Tools in Claude Official Blog](https://claude.com/blog/interactive-tools-in-claude)
