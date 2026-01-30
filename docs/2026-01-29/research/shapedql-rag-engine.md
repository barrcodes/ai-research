# ShapedQL – A SQL engine for multi-stage ranking and RAG

| | |
|---|---|
| **Source** | Shaped 2.0 Blog / Hacker News |
| **URL** | [playground.shaped.ai](https://playground.shaped.ai) |
| **Researched** | 2026-01-29 |

## Overview

ShapedQL is a declarative SQL dialect that consolidates multi-stage ranking pipelines into a unified query language, replacing fragmented infrastructure (vector DBs, feature stores, inference services) with a coherent system. It addresses a critical gap in RAG and recommendation stacks: ranking (finding the best 10 items from thousands) remains an infrastructure problem even as retrieval has been commoditized by vector databases.

## Key Points

- **Unified Stack**: Replaces approximately 500 lines of Python/LangChain boilerplate with declarative SQL queries, eliminating the patchwork of Vector DB + Feature Store + Inference Service
- **Four-Stage Pipeline**: RETRIEVE (hybrid search: BM25 + vectors) → FILTER (business constraints) → SCORE (ML models for ranking) → REORDER (diversity optimization)
- **Sub-50ms Latency**: Serverless endpoints designed for real-time inference requirements, critical for agentic retrieval patterns
- **Explicit Over Implicit**: Moves away from opaque configuration defaults toward transparent, version-controlled relevance logic compatible with GitOps workflows
- **LLM-Native API**: Designed for AI agents to understand and generate—concise, declarative syntax reduces parsing errors vs. traditional configuration formats

## Technical Details

**Architecture**: Three-layer infrastructure combining retrieval, feature engineering, and ranking into a single query compilation step. The system "compiles declarative queries into high-performance, multi-stage ranking pipelines."

**Hybrid Retrieval**: Supports both keyword search (BM25) and vector similarity, merged at the RETRIEVE stage rather than forcing choice between modalities.

**Ranking Stage**: Integrates real-time ML models (click probability, relevance scoring) with personalization context, handling both hard constraints (inventory checks) and soft scoring (user preferences) in a single expression.

**API Surface**: SQL dialect primary, with Python and TypeScript SDKs available. The query syntax is explicit enough for LLMs to generate reliably.

| Aspect | ShapedQL | Traditional Stack |
|--------|----------|-------------------|
| Code Volume | ~50 lines SQL | ~500 lines Python |
| Configuration | Declarative, versioned | Implicit, brittle defaults |
| Retrieval Model | Hybrid (BM25 + vectors) | Choose one or orchestrate externally |
| Ranking Latency | <50ms p99 | Variable, orchestration overhead |
| LLM Compatibility | Native (easy to parse/generate) | Complex abstractions |

## Implications

**For RAG Systems**: This eliminates a major architectural decision point. Instead of stitching together vector retrieval + reranking layers + business logic filters, architects define ranking intent declaratively once. The <50ms guarantee enables agentic patterns where LLM calls trigger retrieval queries in loops.

**For Teams**: Shifts ranking from a tuning problem (tweaking ML model weights, reranker thresholds) to a query optimization problem. Product teams can now iterate on relevance logic through code review rather than A/B testing.

**Trade-offs**: Abstraction over raw control—you don't control the compilation strategy. For highly specialized ranking logic (e.g., multi-objective optimization across conflicting metrics), you're still constrained by the four-stage model.

**When to Use**: Suited for search, recommendations, and agent-driven retrieval where ranking freshness and latency matter. Less applicable if your ranking logic is asymmetric or requires custom orchestration beyond the four stages.

**Positioning**: Shaped reframes itself from "recommendation platform" to "Database for Relevance"—a distinction that matters as AI shifts from human-driven discovery to agentic retrieval as the primary interface.

## Related

- [Show HN: ShapedQL discussion](https://news.ycombinator.com/item?id=46779922) - Technical discussion with founder responses
- [Why We Built a Database for Relevance](https://www.shaped.ai/blog/why-we-built-a-database-for-relevance-introducing-shaped-2-0) - Official architecture and vision post
- [Semantic Reranking with Azure SQL and Cohere](https://devblogs.microsoft.com/azure-sql/semantic-reranking-with-azure-sql-sql-server-2025-and-cohere-rerank-models/) - Competing approach: reranking within SQL Server
- [Graph RAG vs SQL RAG](https://towardsdatascience.com/graph-rag-vs-sql-rag/) - Broader context on structured retrieval patterns
