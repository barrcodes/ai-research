---
title: Claude Launches MCP Apps - Interactive Work OS with Slack, Figma, and Asana
source: Anthropic / Reddit Discussion
url: https://old.reddit.com/r/ClaudeAI/hot/
researched_at: 2026-01-27T00:00:00Z
fetch_method: web-search
---

# Claude Launches MCP Apps - Interactive Work OS with Slack, Figma, and Asana

## Overview

Anthropic released **MCP Apps**, an extension to the Model Context Protocol that transforms Claude from a text-based assistant into an integrated work OS. Instead of switching between applications, users can now interact with Slack, Figma, Asana, Canva, and nine other enterprise tools directly within Claude's chat interface. This represents a fundamental shift in how AI agents can operate across enterprise software ecosystems.

## Key Points

- **MCP Apps Framework**: Extension to the Model Context Protocol enabling servers to deliver interactive UIs (HTML/JavaScript bundles) rendered in sandboxed iframes within the chat window
- **Initial Launch Partners**: 10 integrated apps at launch (Amplitude, Asana, Box, Canva, Clay, Figma, Hex, monday.com, Slack) with Salesforce Agentforce 360 coming soon
- **Bidirectional Communication**: UI-to-host communication via JSON-RPC over postMessage, enabling real-time interaction and state synchronization
- **Availability**: Web and desktop versions for Pro, Team, and Enterprise tiers (Claude Cowork support planned)
- **Security Model**: Sandboxed iframe execution with pre-declared HTML templates, auditable JSON-RPC messaging, and explicit user consent for tool calls

## Technical Details

### Architecture

The implementation uses a **tool with UI metadata pattern**:
1. MCP servers declare tools with a `_meta.ui.resourceUri` field pointing to a UI resource
2. UI Resources are served via the `ui://` scheme as bundled, self-contained HTML/JavaScript packages
3. Hosts (Claude) fetch and render in isolated iframes using sandbox restrictions
4. Bidirectional communication enabled through JSON-RPC protocol over postMessage

### Security Layers

**Iframe Sandboxing**: All UI content runs in restricted iframes preventing direct DOM access to parent context

**Template Declaration**: Hosts can review HTML content before rendering, establishing trust boundaries

**Auditable Messaging**: All UI-to-host communication flows through loggable JSON-RPC, creating an audit trail

**User Consent Gates**: Hosts can require explicit approval for UI-initiated tool calls, preventing unauthorized actions

### Communication Protocol

The framework uses a JSON-RPC dialect that:
- Shares some core MCP protocol messages (e.g., `tools/call`)
- Introduces new `ui/*` prefixed methods for UI-specific operations
- Maintains request/response parity for deterministic state management
- Enables event logging and debugging capabilities

## Implications

### For Enterprise Architecture

1. **Reduced Tab-Switching Friction**: Critical for AI agent productivity - eliminating context switches improves task completion rates and decision quality
2. **Standardization Opportunity**: MCP Apps provides a vendor-neutral way to embed third-party UIs in AI systems, reducing point integrations
3. **Workflow Consolidation**: Native support for Slack search, Asana task creation, and Figma manipulation within agentic workflows eliminates glue code
4. **Auditability at the Protocol Level**: JSON-RPC audit trails provide compliance-friendly logging of all UI interactions

### For LLM Agent Design

1. **Tool Invocation UX**: Moving beyond text-based tool outputs allows agents to present rich interfaces - critical for complex approval workflows
2. **State Visibility**: Agents can see real-time UI state through iframes, enabling more intelligent decision-making based on visual context
3. **Interaction Patterns**: Enables novel agent patterns like "preview before commit" (draft in Slack, approve in UI, then post)
4. **Platform Dependency Risk**: Heavy reliance on individual app partnerships creates single points of failure if integrations regress

### For Integration Strategy

1. **MCP Server Opportunity**: Any vendor can build an MCP server with UI capabilities - lowers barrier vs. native API integration
2. **Security Model Maturity**: Sandbox + template declaration prevents malicious UIs from consuming credentials or exfiltrating data
3. **Competitive Dynamics**: OpenAI's own UI framework adoption (via GPT-4 plugins) suggests industry convergence on MCP-like standards

## Open Questions

- **Performance**: How does iframe rendering impact latency for high-frequency tool calls?
- **State Management**: How do agents handle concurrent UI interactions and race conditions?
- **Adoption Velocity**: Will indie developers build MCP apps faster than Anthropic can negotiate enterprise partnerships?
- **Fallback Behavior**: What happens when UI rendering fails - does the agent revert to text-based tool calling?

## Related

- [MCP Apps - Model Context Protocol Blog](http://blog.modelcontextprotocol.io/posts/2026-01-26-mcp-apps/) - Comprehensive technical specification and design rationale
- [Anthropic extends MCP with a UI framework](https://thenewstack.io/anthropic-extends-mcp-with-an-app-framework/) - Deep dive into architecture decisions
- [MCP Apps - Model Context Protocol Docs](https://modelcontextprotocol.io/docs/extensions/apps) - Official specification
- [GitHub: SEP-1865 Pull Request](https://github.com/modelcontextprotocol/modelcontextprotocol/pull/1865) - Formal protocol extension proposal
- [Anthropic integrates third-party apps into Claude, reshaping enterprise AI workflows](https://www.cio.com/article/4122735/anthropic-integrates-third%E2%80%91party-apps-into-claude-reshaping-enterprise-ai-workflows.html) - Enterprise impact analysis
