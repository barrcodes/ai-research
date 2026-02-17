# Axiom - Local Open-Source AI Research Agent

| | |
|---|---|
| **Source** | Hacker News |
| **URL** | [news.ycombinator.com/item?id=46954141](https://news.ycombinator.com/item?id=46954141) |
| **Researched** | 2026-02-09 |

## Overview

Axiom is an autonomous research agent built in C#/.NET that runs entirely on local infrastructure using Ollama and llama3.1:8b. It accepts a research topic, generates diverse search queries, fetches and analyzes web sources, and synthesizes structured markdown reports with citations. The system prioritizes developer autonomy and ecosystem coverage (particularly .NET) over inference speed.

## Key Points

- **Local-first architecture**: No cloud API dependencies beyond Brave Search (free tier). Runs on consumer hardware with semantic memory via SQLite.
- **Agentic workflow pattern**: Agent generates search queries → fetches sources → analyzes findings → synthesizes report. Supports asynchronous execution with ~15-minute runtime on Ryzen 5 5500.
- **LLM limitations acknowledged**: llama3.1:8b model occasionally generates malformed tool calls, mitigated through retry logic. Quality bounded by search API relevance, not agent capability.
- **Ecosystem positioning**: Targets .NET developers underserved by LangChain/LlamaIndex dominance. C# choice deliberate to address gap, not performance requirement.

## Technical Details

**Architecture Components:**
- Framework: .NET 8 with C#
- Local LLM: Ollama + llama3.1:8b inference
- Memory: SQLite semantic storage (enables context retention across queries)
- Search: Brave Search API integration (free tier sufficient)
- Inference: CPU-only capable (~15 min per research cycle on mid-range processor)

**Agentic Pattern:**
The agent operates as a sequential orchestrator: topic input → query generation (diversity objective) → parallel source fetching → per-source analysis (extract key findings) → synthesis phase (structured markdown with citations). Tool call validation and retry logic handle model brittleness at small sizes.

**Performance Trade-off:**
Single research operation: ~15 minutes on CPU (no GPU). Designed for batch/asynchronous workflows, not real-time interactive research. This constraint is architectural, not accidental—enables local-only operation without compromising model capability.

## Implications

**When to use Axiom:**
- Research automation workflows where API costs and data residency matter more than latency
- .NET teams lacking agentic AI tooling (LangChain/LlamaIndex Python/JS dominance creates friction)
- Batch research operations (intelligence briefings, literature reviews, competitive analysis) where 15-minute runtime acceptable
- Organizations requiring full local control and offline capability

**Trade-offs vs. cloud agents:**
- Speed: ~15 min vs. minutes (cloud) or seconds (fine-tuned models)
- Cost: GPU hardware amortization vs. per-request API billing
- Control: Full data privacy, no external dependencies vs. vendor lock-in mitigation
- Quality: Limited by 8B model constraints vs. 70B+ alternatives in cloud tier

**Architectural patterns worth noting:**
1. **Semantic memory (SQLite)** enables context reuse across independent research queries—valuable for multi-phase investigations
2. **Retry logic on tool calls** signals that 8B models require explicit error handling in production agentic systems
3. **Free-tier API integration** (Brave Search) reduces operational complexity; teaches cost-aware design
4. **Async execution model** decouples research from interactive workflows, enabling parallel batch processing

**Alternatives comparison:**
- LangChain/LlamaIndex: More mature ecosystem, but Python/JavaScript centric
- Cloud agents (Claude Projects, OpenAI): Faster, higher-quality synthesis, but API dependency and cost
- Specialized research tools (Perplexity, you.com): Polished UX but proprietary, no local option

## Sources

- [Show HN: Axiom – Open-source AI research agent that runs locally (C#, Ollama) | Hacker News](https://news.ycombinator.com/item?id=46954141) - Original HN discussion with creator commentary
- [GitHub - aasherkamal216/Axiom: A documentation AI Agent built with LangGraph, MCP Docs, and Chainlit](https://github.com/aasherkamal216/Axiom) - Related agentic project reference
- [kyrolabs/awesome-agents: Awesome list of AI Agents](https://github.com/kyrolabs/awesome-agents) - Broader agentic systems context
