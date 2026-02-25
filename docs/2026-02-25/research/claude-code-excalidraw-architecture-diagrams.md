# I Built a Claude Code Skill That Auto-Generates Architecture Diagrams on a Live Excalidraw Canvas

| | |
|---|---|
| **Source** | Reddit r/ClaudeAI |
| **URL** | [reddit.com/r/ClaudeAI/comments/1rdzhk6/](https://www.reddit.com/r/ClaudeAI/comments/1rdzhk6/i_built_a_claude_code_skill_that_autogenerates/) |
| **Researched** | 2026-02-25 |

## Overview

A Claude Code skill enables real-time architecture diagram generation by connecting Claude to a live Excalidraw canvas. Rather than static image output, the skill analyzes codebases or system descriptions and renders diagrams interactively, allowing immediate editing and refinement. The workflow bridges the gap between LLM code analysis and visual system design.

## Key Points

- **Live Canvas Rendering**: Uses Excalidraw MCP (Model-Controlled-Process) server to draw elements in real-time within a browser canvas rather than generating static images
- **Intelligent Component Analysis**: Claude analyzes codebases or descriptions to identify components, services, databases, and relationships automatically
- **Visual Language Convention**: Color-codes by role (databases green, APIs purple, frontends blue) for consistent visual language across diagrams
- **Layout Flexibility**: Supports multiple layout strategies including vertical, horizontal, and hub-and-spoke patterns
- **Export Capabilities**: Diagrams can be exported to PNG, SVG, or shareable Excalidraw links for collaboration
- **Open-Source**: Project is publicly available on GitHub for community contribution and customization

## Technical Details

The skill operates through a three-step workflow:

1. **Prompt Input**: User provides high-level description ("Draw a diagram of this project's architecture") or points to a codebase
2. **Analysis Phase**: Claude parses code to identify architectural components, their relationships, and data flows
3. **Rendering Phase**: MCP server executes drawing commands in real-time—adding shapes, arrows, and labels directly to the Excalidraw canvas

The approach leverages Claude's code comprehension abilities combined with Excalidraw's interactive canvas API. By using the MCP protocol, Claude can maintain a stateful connection to the diagram canvas, enabling iterative refinement where diagrams can be modified in response to follow-up queries.

## Implications

**For Claude Code Users**: This demonstrates a pattern where Claude Code skills can bridge AI analysis with external design tools, reducing context-switching and manual diagram creation. It suggests broader possibilities for real-time visualization workflows.

**For Architecture Documentation**: Enables living architecture diagrams that stay synchronized with codebase changes, addressable through natural language queries. Reduces friction in keeping documentation current.

**For Knowledge Work**: Shows how agentic patterns can automate tedious visualization tasks without requiring specialized drawing skills. The real-time rendering approach feels more interactive than batch-generated diagrams.

**Technical Consideration**: The MCP-based approach is architecturally cleaner than file-based workflows—it maintains a live connection to the drawing tool rather than serializing state. This opens doors for collaborative diagram sessions where multiple Claude instances could contribute to the same canvas.

## Sources

- [GitHub: edwingao28/excalidraw-skill](https://github.com/edwingao28/excalidraw-skill) - Official open-source repository
- [Reddit r/ClaudeAI Discussion](https://www.reddit.com/r/ClaudeAI/comments/1rdzhk6/i_built_a_claude_code_skill_that_autogenerates/) - Original post with community feedback
