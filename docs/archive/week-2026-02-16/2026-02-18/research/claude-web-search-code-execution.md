# Claude Web Search Now Executes Code Before Tool Results

| | |
|---|---|
| **Source** | Anthropic Blog / Claude API Release |
| **URL** | [claude.com/blog/improved-web-search-with-dynamic-filtering](https://claude.com/blog/improved-web-search-with-dynamic-filtering) |
| **Researched** | 2026-02-18 |

## Overview

Anthropic released dynamic filtering capabilities for Claude's web search and web fetch tools on February 17, 2026. Claude can now natively write and execute code during web searches to filter results before they reach the context window, improving accuracy by up to 11% while reducing token consumption by 24%. This represents a fundamental shift in how agentic systems can interact with external data sources.

## Key Points

- **In-Flight Code Execution**: Claude executes sandboxed code during web search/fetch operations to dynamically filter results before context loading, keeping only relevant information and discarding the rest
- **Significant Performance Gains**: Accuracy improvements across benchmarks—BrowseComp improved for Sonnet 4.6 from 33.3% to 46.6%, and Opus 4.6 from 45.3% to 61.6%
- **Token Efficiency**: 24% reduction in input tokens while simultaneously improving output quality—a rare win-win in LLM economics
- **API Code Execution is Now Free**: When used with web search or web fetch tools, eliminating a barrier to widespread adoption of code execution in agentic workflows
- **Enabled by Default**: Dynamic filtering ships enabled on the Claude API with new tool versions (web_search_20260209, web_fetch_20260209)
- **Tool Graduation**: Code execution tool promoted to general availability after successful beta testing

## Technical Details

**Implementation Architecture:**
- Tools execute code in a sandboxed environment during the request lifecycle
- Code runs **before** search results populate the context window
- Uses beta header `code-execution-web-tools-2026-02-09` for API access
- Supports both web_search_20260209 and web_fetch_20260209 versions
- Works natively with Claude Opus 4.6 and Sonnet 4.6

**Benchmarks Used:**
- BrowseComp: Evaluates web search and navigation capabilities
- DeepsearchQA: Measures complex information retrieval and synthesis
- Average 11% accuracy improvement across both benchmarks

**Economic Impact:**
- Previous barrier: Code execution incurred separate API costs
- Current model: Free when integrated with web tools (major adoption catalyst)
- Combined effect: Better accuracy + lower token costs + no execution charges = powerful incentive for agentic adoption

## Implications

**For Practitioners:**

1. **Agent Architecture Redesign**: This changes fundamental assumptions about agentic design. Code execution mid-flow means agents can now intelligently filter before context inflation—critical for long-running information retrieval tasks.

2. **Context Window Optimization**: With 24% fewer tokens consumed while improving accuracy, this directly impacts cost and latency for production agents. At scale, this is a significant operational cost reduction.

3. **Reliability Improvements**: The 11% accuracy improvement suggests Claude's ability to selectively attend to relevant data improves results more than brute-force context expansion. Agentic systems should anticipate needing less prompt engineering for data filtering.

4. **Free Code Execution Unlocks New Patterns**: Previously, every code execution invocation was a separate charge. Bundling it free with web tools normalizes code execution as a default part of agent workflows rather than an optimization.

5. **Competitive Positioning**: This is architecturally subtle but strategically significant—competitors offering only raw search results without in-context filtering will see diminishing returns. The ability to execute code as part of the search itself (not as a post-processing step) is a capabilities moat.

6. **Integration Points**: Teams building agentic systems should audit their web search patterns to see where this dynamic filtering can replace custom post-processing code, especially for content filtering, entity extraction, or relevance ranking.

## Sources

- [Improved Web Search with Dynamic Filtering | Claude](https://claude.com/blog/improved-web-search-with-dynamic-filtering)
- [Web search tool - Claude API Docs](https://platform.claude.com/docs/en/agents-and-tools/tool-use/web-search-tool)
- [Code execution tool - Claude API Docs](https://platform.claude.com/docs/en/agents-and-tools/tool-use/code-execution-tool)
- [Anthropic Upgrades Claude AI Web Search Tools With 11% Accuracy Boost](https://bitcoinethereumnews.com/tech/anthropic-upgrades-claude-ai-web-search-tools-with-11-accuracy-boost/)
- [Web fetch tool - Claude API Docs](https://platform.claude.com/docs/en/agents-and-tools/tool-use/web-fetch-tool)
