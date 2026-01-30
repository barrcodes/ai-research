# Code a Simple RAG from Scratch

| | |
|---|---|
| **Source** | Hugging Face Blog (ngxson) |
| **URL** | [huggingface.co/blog/ngxson/make-your-own-rag](https://huggingface.co/blog/ngxson/make-your-own-rag) |
| **Researched** | 2026-01-26 |

## Overview

This tutorial demonstrates building a functional RAG (Retrieval-Augmented Generation) system in Python without specialized vector database infrastructure. The walkthrough uses Ollama for local model execution, implementing core RAG patterns: embedding-based retrieval, similarity ranking, and context-augmented generation. The approach emphasizes that production-grade RAG doesn't require complex infrastructure—simple patterns work effectively at smaller scales.

## Key Points

- **Three-component architecture**: Embedding model (vectorizes text), vector database (stores vectors), language model (generates responses)
- **Two-phase operation**: Indexing phase chunks and embeds the dataset; retrieval phase embeds queries and ranks chunks by semantic similarity
- **In-memory implementation**: Uses Python lists for vector storage—acceptable for prototyping but requires production vector databases (Qdrant, Pinecone, pgvector) at scale
- **Cosine similarity metric**: Calculates semantic relevance as `(A·B) / (||A|| × ||B||)`—ranges 0-1, higher values indicate better matches
- **Prompt engineering integration**: System instructions enforce constraint that LLM uses only retrieved context, preventing hallucination

## Technical Details

### Implementation Stack
- **Embedding Model**: BGE-base (CompendiumLabs/bge-base-en-v1.5-gguf) for semantic vectorization
- **Language Model**: Llama-3.2-1B for efficient generation in resource-constrained environments
- **Execution Runtime**: Ollama for local, cloud-free model serving
- **Dependencies**: Single pip package (`ollama`); no database clients needed

### Critical Functions

```python
# Similarity scoring
def cosine_similarity(a, b):
    dot_product = sum(x * y for x, y in zip(a, b))
    norm_a = sum(x ** 2 for x in a) ** 0.5
    norm_b = sum(x ** 2 for x in b) ** 0.5
    return dot_product / (norm_a * norm_b)

# Retrieval with ranking
def retrieve(query, top_n=3):
    query_embedding = ollama.embed(model=EMBEDDING_MODEL, input=query)['embeddings'][0]
    similarities = [(chunk, cosine_similarity(query_embedding, embedding))
                    for chunk, embedding in VECTOR_DB]
    return sorted(similarities, key=lambda x: x[1], reverse=True)[:top_n]
```

### Performance Considerations

| Challenge | Implication | Solution |
|-----------|-------------|----------|
| Single-metric retrieval | Fails on multi-topic queries; low diversity | Implement reranking or query expansion |
| Linear scan retrieval | O(n) latency per query; breaks at 10K+ chunks | Vector indices (HNSW, IVF) via production DB |
| Chunk size optimization | Poor boundary splits reduce context quality | Semantic chunking or domain-specific segmentation |
| Model capacity limits | Small models may struggle with complex reasoning | Scale to larger models; implement intermediate steps |

## Implications

**For prototyping**: This approach validates RAG architecture and tuning strategies with zero infrastructure overhead. Useful for proof-of-concept work, teaching, and quick iterations on embedding models or prompting strategies.

**Production tradeoffs**: In-memory vectors work effectively up to ~1K chunks (depends on embedding dimensions and available RAM). Beyond that, migrate to:
- **Pinecone** for managed vector search
- **Qdrant** for self-hosted control with HNSW indexing
- **pgvector** for PostgreSQL-native vector operations

**Architectural decisions**:
- Streaming generation (via `stream=True`) improves perceived latency for interactive applications
- Prompt constraints ("use only provided context") are essential—LLMs otherwise hallucinate beyond retrieved scope
- Reranking models (cross-encoders) significantly improve quality but add latency; useful when top-1 retrieval matters

**When to use this pattern**: Single-document QA, FAQ systems, small knowledge bases (< 5K documents), demonstrations, and educational settings. For production multi-source systems, add proper vector indexing and monitoring.

## Related

- [Retrieval-Augmented Generation for Large Language Models: A Survey](https://arxiv.org/abs/2312.10997) - Comprehensive taxonomy of RAG architectures and techniques
- [Ollama Documentation](https://ollama.ai/) - Local model serving reference
- [BGE Models on Hugging Face](https://huggingface.co/BAAI/bge-base-en-v1.5) - Production-grade embedding models
- [Vector Database Benchmarks](https://huggingface.co/spaces/mteb/leaderboard) - Evaluation harness for embeddings across tasks
