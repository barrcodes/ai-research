# Docdex: A Local Tool to Reduce LLM Tokens and Make Agents Smarter

| | |
|---|---|
| **Source** | GitHub Repository |
| **URL** | [github.com/bekirdag/docdex](https://github.com/bekirdag/docdex) |
| **Researched** | 2026-02-20 |

## Overview

Docdex is a local-first code indexing daemon that serves structured repository context to AI agents via MCP protocol. Rather than uploading code to cloud RAG systems or relying on naive grep-based search, it builds Abstract Syntax Trees (ASTs) and dependency graphs to deliver semantically ranked, minimal-context results—directly reducing LLM token consumption while maintaining code privacy.

## Key Points

- **Local-first architecture**: Daemon runs on developer machines, eliminating external service dependencies and code exposure. Respects air-gap requirements for sensitive codebases.

- **AST-driven indexing**: Builds dependency graphs and impact analysis—finds function definitions, tracks callers/callees, returns semantically ranked results instead of literal text matches. This structural understanding enables aggressive context pruning.

- **MCP protocol integration**: Exposes HTTP/SSE endpoints (`http://localhost:28491/v1/mcp`) compatible with Claude Desktop, Cursor, Windsurf, Cline. Auto-configures on install; agents invoke via standard MCP resource/tool calls.

- **Multi-language support**: Handles Rust, Python, JavaScript, Go, Java, C++, and others—single daemon serves multiple repos simultaneously with per-repo isolation (`x-docdex-repo-id` headers).

- **Agent and repo memory layers**: Stores project-specific facts and user preferences, enabling agents to learn context across sessions without recomputing or re-embedding.

## Technical Details

**Architecture**: Repository → Indexer (AST/graph build) → Daemon (HTTP service) → MCP Client → AI Agent

**Key endpoints**:
- `/v1/ast` — Definition lookup and scope queries
- `/v1/graph/impact` — Downstream dependency tracking
- `/v1/initialize` — Dynamic repo mounting
- `/search` — Unified semantic search

**Integration point**: MCP protocol over HTTP/SSE allows any compatible AI client to request structured code context. The daemon returns ranked, scope-aware results rather than raw file embeddings.

**Requirements**: Node.js >= 18; Windows users need Visual C++ 2015-2022 redistributable.

## Implications

**For agentic patterns**: This shifts the constraint from "how much context can we afford" to "what context is semantically necessary." Agents querying Docdex receive smaller, higher-quality context windows—fewer hallucinations, faster inference, lower token spend per interaction.

**For enterprise workflows**: Local execution addresses security concerns around code uploads. The MCP integration means adoption is seamless for teams already using Claude Desktop or Cursor—no custom client code needed.

**When to use**: Ideal for teams building AI-assisted developer tools, code-generation agents, or multi-modal IDEs. Best value in codebases >10K LOC where grep noise becomes severe. Less critical for single-file scripts or documentation-heavy projects where semantic ranking yields minimal improvement.

**Trade-offs**: Requires daemon process overhead (minor) and initial indexing time (one-time amortized cost). No documented benchmarks provided; adoption decisions should include POC testing against your codebase size and agent query patterns.

## Sources

- [Docdex GitHub Repository](https://github.com/bekirdag/docdex) - Complete project source and MCP protocol implementation
