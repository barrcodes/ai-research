# A Framework and Leaderboard for Retrieval Pipelines Evaluation on ViDoRe v3

| | |
|---|---|
| **Source** | Hugging Face Blog (antoineedy) |
| **URL** | [huggingface.co/blog/antoineedy/vidore-v3-pipeline-framework-and-leaderboard](https://huggingface.co/blog/antoineedy/vidore-v3-pipeline-framework-and-leaderboard) |
| **Researched** | 2026-02-27 |

## Overview

ViDoRe v3 is a production-grade benchmark for evaluating retrieval pipelines on real-world enterprise document retrieval tasks. The release introduces a standardized Python framework (`BasePipeline`) that lets engineers build, benchmark, and compare retrieval systems against 8 public datasets with reproducible metrics for accuracy, latency, and multimodal performance.

## Key Points

- **Real-world benchmark**: 8 enterprise domain datasets with PDF page images and OCR-extracted text, covering diverse document types
- **Pluggable pipeline framework**: Extend `BasePipeline` to implement custom indexing and search logic; evaluate with a single CLI command
- **Multimodal evaluation**: Compares OCR-only, image-only (VLM), and hybrid approaches—hybrid typically shows ~6.5% accuracy gains over image-only
- **Leaderboard evidence**: Jina v4 embeddings + ZeRank2 reranking dominates text-based retrievers; ColEmbed and Llama Nemotron show strong efficiency trade-offs
- **Latency as first-class metric**: Measures indexing and search throughput to surface the accuracy-speed trade-off, not just top-1 performance

## Technical Details

The framework normalizes pipeline construction across five core stages:

| Stage | Role | Decision Point |
|-------|------|---|
| **OCR/Extraction** | Text from images/PDFs | Trade visual context loss for speed |
| **Embedding** | Vectorization (text/image/multimodal) | Model capacity vs. latency; unimodal vs. VLM |
| **Chunking** | Content segmentation | Chunk size impacts context window utilization |
| **Query Processing** | Expansion, rewriting, pseudo-doc generation | Helps out-of-domain generalization |
| **Retrieval & Reranking** | Hybrid search (RRF), late interaction (MaxSim), cross-encoder reranking | Stacking layers improves precision but adds latency |

Top performers combine:
- Dense embedding (Jina v4, ColEmbed 8B v2, or Llama Nemotron)
- Optional hybrid search (sparse + dense via RRF)
- Late-interaction mechanisms (token-level scoring) for high-precision domains
- Reranker as a pre-LLM filter when corpus is large

For ultra-low-latency scenarios (e.g., mobile, edge), mxbai-edge and Llama Nemotron VL achieve <100ms indexing and search with acceptable recall losses.

## Implications

**For RAG system architects:**
- Use the leaderboard to ground embedding and reranker selection instead of generic MTEB scores—ViDoRe captures real enterprise retrieval patterns
- Multimodal (image + OCR) is worth the overhead for document-heavy domains; VLM-only approaches leave accuracy on the table
- Late-interaction and hybrid search are now table-stakes for top-tier accuracy; single-stage dense retrieval is a speed play, not a quality play
- Latency metrics matter: acknowledge that Jina v4 + ZeRank2 is slower than Nemotron 1B; pick based on SLA, not just leaderboard position

**For agentic RAG workflows:**
- Consider retrieval agents (multi-step tool use) as a pipeline component when queries require disambiguation, multi-hop lookup, or query rewriting; the benchmark framework supports this as an extension
- Chunking and query expansion become critical when building retrievers that feed LLM reasoning loops—over-chunking dilutes context, under-chunking misses relevant sections

**For evaluation and iteration:**
- Adopt the `BasePipeline` interface as your internal standard; port your existing retrievers into the framework to get reproducible comparisons across your corpus
- Benchmark against the 8 public datasets first to validate assumptions, then add proprietary datasets incrementally
- Track both raw accuracy and latency percentiles; a system with 85% NDCG at 50ms is often more valuable than 92% NDCG at 500ms

## Sources

- [ViDoRe v3 Pipeline Leaderboard](https://huggingface.co/spaces/vidore/vidore-leaderboard?tab=vidore-v3-pipeline) - Live rankings and pipeline comparisons
- [ViDoRe Benchmark GitHub Repository](https://github.com/illuin-tech/vidore-benchmark/) - Open-source implementation and dataset access
- [ViDoRe v3 Research Paper](https://arxiv.org/abs/2601.08620) - Full evaluation methodology and results
