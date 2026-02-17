# PageMap – MCP Server That Compresses Web Pages from 100K to 2-5K Tokens

| | |
|---|---|
| **Source** | GitHub - Retio-ai/Retio-pagemap |
| **URL** | [github.com/Retio-ai/Retio-pagemap](https://github.com/Retio-ai/Retio-pagemap) |
| **Researched** | 2026-02-17 |

## Overview

PageMap is an MCP (Model Context Protocol) server that compresses web pages into interactive, token-efficient maps for AI agents. By applying a 5-stage compression pipeline—HTMLRAG preprocessing, semantic filtering, schema-aware chunking, and attribute stripping—it reduces typical 100K-token pages to 2-5K tokens while preserving actionability. This compression unlocks multi-step web automation within a single LLM context window, a capability competitors lack.

## Key Points

- **5.6x token efficiency over Playwright MCP**, 2-10x over Firecrawl/Jina Reader, with 95.2% task success vs. 39-61% for alternatives
- **Interactive-first architecture**: Three-tier interactive element detection (ARIA roles → implicit HTML roles → Chrome DevTools Protocol listeners) enables agents to click, type, select—not just read
- **Dual-pipeline design**: Playwright captures accessibility tree + raw HTML; compression pipeline handles semantic filtering, script extraction (JSON-LD, RSC payloads), and budget-aware output assembly
- **Security hardened**: Built-in SSRF defense (IP blocking, scheme validation), prompt injection protection (nonce-based boundaries), action sandboxing, input validation with timeouts
- **Multilingual from the start**: Auto-detects locale from URL domain; supports Korean, English, Japanese, French, German with locale-specific formatting

## Technical Details

**Compression Pipeline:**
The 5-stage approach removes navigation, footers, sidebars, and unused attributes while extracting structured metadata and interactive element references with numbered tags. Output includes compressed HTML, images, interactive element map, and schema metadata—all within token budget.

**Interactive Detection:**
- Tier 1: Elements with ARIA roles and accessible names
- Tier 2: Implicit HTML roles (inputs, selects, textareas, buttons)
- Tier 3: Divs/spans with Chrome DevTools Protocol event listeners

**Performance Benchmark:**
Against Playwright MCP, Firecrawl, and Jina Reader on a 66-task evaluation:

| Metric | PageMap | Playwright MCP | Firecrawl | Jina Reader |
|--------|---------|---|---|---|
| Tokens per page | 2-5K | 50-540K | 10-50K | 10-50K |
| Task success | 95.2% | 39.7% | 60.9% | 61.2% |
| Cost per 66 tasks | $0.58 | $6.71 | $2.66 | $1.54 |

## Implications

**For agentic systems**, PageMap removes a critical constraint: fitting web interaction into context. Previous approaches forced a choice between reading capability (cramming full HTML) and token budget. PageMap enables both—agents can navigate, fill forms, extract data, and iterate within one context window.

**Trade-offs:** PageMap depends on Playwright's browser engine (Chromium) vs. Firecrawl/Jina's text extraction. This increases memory/CPU but gains interactive fidelity. For read-only use cases, lighter solutions suffice; for automation, this cost is justified.

**When to use:** Real-world agent workflows—research with navigation, e-commerce checkout flows, form submission, comparison shopping. The compression makes long-horizon web tasks practical where they were previously token-prohibitive.

**Architectural decision point:** If you're building agentic products requiring web interaction, PageMap's MCP integration means one-line adoption vs. rolling custom compression logic. The security hardening (SSRF, injection defense) suggests production readiness.

## Sources

- [Retio-ai/Retio-pagemap on GitHub](https://github.com/Retio-ai/Retio-pagemap) - Official repository with benchmarks, technical architecture, and usage examples
