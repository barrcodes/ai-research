---
title: Show HN - Figma MCP Without Tool Explosion – Let AI Execute JavaScript
source: GitHub (youware-labs/figma-pilot)
url: https://github.com/youware-labs/figma-pilot
researched_at: 2026-01-29T00:00:00Z
---

# Figma MCP Without Tool Explosion – Let AI Execute JavaScript

## Overview

Figma-Pilot is an MCP server that inverts the traditional tool proliferation problem by offering only three tools—particularly `figma_execute`, which accepts JavaScript code for direct execution against the Figma API. This reduces tool definition tokens by 90%+ while enabling agents to perform complex batch operations, filtering, and conditional logic that granular tools cannot support.

## Key Points

- **Three-tool design** replaces dozens of granular tools, eliminating context overhead from verbose tool definitions
- **JavaScript execution model** allows agents to write Figma plugin code directly, leveraging `figma.query()`, `figma.create()`, `figma.modify()` within a single context
- **Batch operations** enable modification of hundreds of elements in one execution, impossible with individual API-call tools
- **Local HTTP bridge** (port 38451) connects the MCP server to the Figma desktop plugin, enabling bidirectional control via stdio
- **Full Figma API access** within the plugin sandbox—components, accessibility, design tokens, filtering, and transformations all available

## Technical Details

The architecture uses three components:
- **MCP Client** → **MCP Server** (Node.js) → **HTTP** (localhost:38451) → **Figma Plugin**

Rather than exposing individual tools like `create_rectangle`, `set_fill_color`, `query_elements`, agents receive a single powerful interface: write JavaScript that executes in Figma's trusted plugin context. This preserves the sandbox boundary while eliminating the abstraction tax of tool-per-operation models.

The `figma_execute` tool accepts async JavaScript, enabling loops, conditionals, and error handling. Results filter before returning to the context window, keeping token usage low while maintaining expressiveness.

## Implications

**For agentic design:** This pattern challenges the assumption that more tools = more capable agents. Instead, fewer powerful tools + code execution = higher capability with lower token cost. Consider this approach when:
- Operations are compositional or batch-oriented
- The execution environment is sandboxed and trustworthy
- Tool definitions create significant overhead

**Trade-offs:** Agents need JavaScript competency (mitigated by LLM training), and the execution sandbox must be secure. Not applicable to untrusted agent code.

**When to apply:** Ideal for domain APIs (Figma, CAD, design tools, databases) where users already work in a specific language. Less suitable for APIs designed as REST endpoints where token cost matters less than discoverability.

## Related

- [MCP Protocol](https://modelcontextprotocol.io/) - Standard for AI tool integration
- Model Context Protocol specification - Defines tool definition overhead being addressed
