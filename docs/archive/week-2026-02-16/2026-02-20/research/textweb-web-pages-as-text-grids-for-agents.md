# TextWeb - Render Web Pages as 2-5KB Text Grids for AI Agents

| | |
|---|---|
| **Source** | GitHub (chrisrobison/textweb) |
| **URL** | [github.com/chrisrobison/textweb](https://github.com/chrisrobison/textweb) |
| **Researched** | 2026-02-20 |

## Overview

TextWeb converts web pages into compact text-grid representations (2-5KB) optimized for LLM comprehension, eliminating vision model overhead. Interactive elements receive numbered references, allowing agents to navigate and manipulate pages via text commands rather than expensive screenshot + vision processing.

## Key Points

- **Text-native rendering**: Full page layout rendered as character grids with spatial awareness, preserving visual hierarchy without screenshots
- **Numbered reference system**: Interactive elements tagged `[ref]` (e.g., `[3]link text`, `[5 button]`) enable deterministic agent navigation via simple text commands
- **Real browser execution**: Uses Playwright/Chromium to handle JavaScript, CSS, dynamic content, and modern web frameworks—not a parser
- **Dramatic cost reduction**: 2-5KB payloads vs. 1MB+ screenshots eliminate vision model API calls entirely
- **Multiple integration patterns**: MCP servers, function calling, LangChain/CrewAI, REST API, Node.js library provide architectural flexibility

## Technical Details

**Four-stage architecture:**

1. Playwright executes page with full JavaScript/CSS support
2. DOM traversal captures element positions, dimensions, text, and interactivity metadata
3. Pixel coordinates map to character grid positions maintaining spatial relationships
4. Interactive elements receive `[ref]` annotations linked to actual DOM nodes for execution

**Grid conventions:**
- Links: `[ref]text`
- Buttons: `[ref button text]`
- Checkboxes: `[ref:X]` or `[ref: ]` (checked/unchecked)
- Text inputs: `[ref:placeholder____]`

**Payload efficiency**: Text-native processing eliminates vision model latency and API costs, making web-interactive agents economically viable at scale.

## Implications

**When to use**: Web automation agents, accessibility systems, cost-sensitive browsing tasks, multi-step research workflows. Ideal where page structure matters more than pixel-perfect rendering.

**Trade-offs**: Cannot handle CAPTCHA or image-dependent navigation (no vision fallback in base implementation). Text layout may degrade on highly visual sites (e.g., image galleries, maps). Suitable for text-heavy, structured interfaces.

**Architectural significance**: Shifts agentic web interaction from vision-heavy to text-native paradigms. Enables lightweight agents in constrained environments and reduces per-interaction costs. MCP integration makes this compatible with Claude Desktop and emerging IDE-embedded AI workflows.

**Decision point**: Compare against screenshot + vision for pages requiring visual interpretation; TextWeb wins on cost and latency for information-extraction and form-filling workflows.

## Sources

- [GitHub: chrisrobison/textweb](https://github.com/chrisrobison/textweb) - Source repository with implementation and integration examples
