# Ghidra MCP Server

| | |
|---|---|
| **Source** | GitHub / LaurieWired |
| **URL** | [github.com/LaurieWired/GhidraMCP](https://github.com/LaurieWired/GhidraMCP) |
| **Researched** | 2026-02-04 |

## Overview

GhidraMCP exposes the NSA's Ghidra reverse engineering platform via the Model Context Protocol, enabling LLMs to autonomously analyze binaries without manual interaction. This bridges agentic AI patterns with professional-grade binary analysis, allowing Claude or other MCP-compatible clients to decompile, rename structures, and enumerate program artifacts programmatically.

## Key Points

- **Two-component architecture**: Java-based Ghidra plugin (HTTP server) + Python MCP bridge translating between protocols
- **Core capabilities**: Decompilation, automated renaming of methods/data structures, enumeration of classes/imports/exports
- **Multi-client support**: Works with Claude Desktop, Cline, 5ire, and any MCP-compatible platform via stdio or SSE transports
- **Minimal friction setup**: Install as Ghidra extension, configure MCP client with bridge endpoint
- **Apache 2.0 licensed**: Production-ready project with established release cycle (v1.4 as of June 2025)

## Technical Details

**Architecture Decision**: The two-component design (Ghidra plugin + Python bridge) decouples the reverse engineering engine from MCP protocol handling. The plugin exposes HTTP endpoints; the bridge translates MCP tool calls to HTTP requests and marshals responses back. This separation allows Ghidra to remain unmodified while supporting multiple LLM clients.

**Integration Pattern**: LLM agents invoke MCP tools to query program structure (method listings, imports), request decompilation of specific functions, or trigger automated analysis. Ghidra executes analysis in its familiar context, returning results that agents can process for further decisions.

**Implementation Stack**: Java (86.2%) for the plugin, Python (13.8%) for the bridge. Maven handles plugin packaging; standard Python MCP SDK provides protocol implementation.

## Implications

**For security teams**: Enables autonomous vulnerability discovery workflows where agents crawl binaries, identify suspicious patterns, and generate analysis reports. Reduces manual disassembly time for large-scale analysis tasks.

**For LLM applications**: Demonstrates how to wrap specialized domain tools (reverse engineering, static analysis, code generation) as MCP servers. This pattern scales to other tool ecosystems—debuggers, static analyzers, compliance checkers.

**Trade-offs to consider**:
- Requires Ghidra to run continuously; can't scale to many parallel analyses on a single instance
- Network latency between client and Ghidra instance adds overhead
- Agentic patterns may produce false positives without human review loops built in

**Architectural significance**: Shows how MCP standardizes AI-tool integration, moving beyond ad-hoc plugins toward protocol-driven extensibility. This is the inverse of embedding AI in tools (like Copilot in IDEs)—it embeds tools in AI systems.

## Sources

- [GhidraMCP Repository](https://github.com/LaurieWired/GhidraMCP) - Primary source
- [Model Context Protocol](https://modelcontextprotocol.io) - Protocol specification
- [Ghidra Project](https://ghidra-sre.org) - Underlying reverse engineering platform
