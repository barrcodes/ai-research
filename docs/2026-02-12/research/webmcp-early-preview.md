# WebMCP Early Preview

| | |
|---|---|
| **Source** | Chrome for Developers Blog & Bug0 |
| **URL** | [developer.chrome.com/blog/webmcp-epp](https://developer.chrome.com/blog/webmcp-epp?hl=en) |
| **Researched** | 2026-02-12 |

## Overview

WebMCP (Web Model Context Protocol) brings Anthropic's Model Context Protocol to the browser, enabling AI agents to interact with websites through explicitly declared APIs rather than DOM scraping. Google released an early preview in Chrome 146, establishing a standard for exposing structured tools that agents can invoke with speed, reliability, and precision—eliminating the need for agents to simulate user behavior.

## Key Points

- **Two-API Model**: WebMCP provides a declarative API for standard HTML form-based actions and an imperative API via `navigator.modelContext` for complex, dynamic workflows
- **No Backend Required**: Tools execute directly in the browser's JavaScript context using the page's existing authentication—no separate MCP server infrastructure needed
- **Human-in-the-Loop Controls**: Sensitive operations can request user confirmation via `requestUserInteraction()` before execution
- **Use-Case Driven**: Initial targets include e-commerce (product search/checkout), travel (flight booking), and customer support (auto-populated tickets)
- **Early Stage**: Currently available via opt-in early preview; W3C spec is a Draft Community Group Report, not standardized yet

## Technical Details

**Registration API**: Developers register tools using `navigator.modelContext.registerTool()` with name, description, JSON Schema input specification, and async handler function:

```javascript
navigator.modelContext.registerTool({
  name: 'capture_console_errors',
  description: 'Capture recent console errors',
  inputSchema: {
    type: 'object',
    properties: {
      severity: { type: 'string', enum: ['error', 'warn', 'all'] },
      limit: { type: 'number' }
    },
    required: ['severity']
  },
  handler: async ({ severity, limit = 50 }) => {
    return { entries: logs, count: logs.length };
  }
});
```

**Security Model**: HTTPS required for production; `http://localhost` works during development. Tools inherit the page's security context and authentication state, eliminating credential duplication.

## Implications

**For Service Builders**: WebMCP shifts from AI-as-user (brittle DOM interaction) to AI-as-API-consumer (structured, explicit). This requires minimal changes—declarative tools in HTML forms need no JavaScript; imperative handlers leverage existing page logic.

**For Architecture Decisions**: The in-browser execution model eliminates MCP server deployment complexity but ties agents to browser constraints (HTTPS, authentication scoping, execution limits). For high-volume or sensitive operations, traditional server-side MCP remains appropriate.

**Adoption Path**: Early preview status means API evolution is expected. Developers should prototype but expect breaking changes before standardization. Most immediate value accrues to web applications with complex interaction flows (checkout, booking, support triage).

## Sources

- [Chrome for Developers Blog - WebMCP Early Preview](https://developer.chrome.com/blog/webmcp-epp?hl=en) - Official announcement and API overview
- [Bug0 - WebMCP Chrome 146 Guide](https://bug0.com/blog/webmcp-chrome-146-guide) - Technical implementation details and examples
- [Google Previews WebMCP](https://searchengineland.com/google-releases-preview-of-webmcp-how-ai-agents-interact-with-websites-469024) - Protocol overview and use cases
