# How We Built a Semantic Highlight Model To Save Token Cost for RAG

| | |
|---|---|
| **Source** | Hugging Face Blog (Zilliz) |
| **URL** | [huggingface.co/blog/zilliz/zilliz-semantic-highlight-model](https://huggingface.co/blog/zilliz/zilliz-semantic-highlight-model) |
| **Researched** | 2026-01-26 |

## Overview

Zilliz developed a 0.6B encoder-only neural model that identifies semantically relevant sentences in RAG-retrieved documents, reducing token costs by 70-80% while improving answer quality. The bilingual (English/Chinese) model outperforms existing solutions by understanding semantic relevance rather than keyword matching, addressing a critical inefficiency where most retrieved tokens contain noise irrelevant to the query.

## Key Points

- **Core Problem**: Traditional RAG retrieves 10+ documents (~10,000+ tokens) but only dozens of sentences are semantically relevant; keyword-based highlighting misses context without explicit query terms
- **Architecture**: Lightweight encoder-only model (BGE-M3 Reranker v2 base) that scores each token in parallel, then averages within sentences to identify high-relevance content
- **Training Innovation**: 5M bilingual samples annotated with LLM reasoning chains included as training signal, enabling self-verification and reducing annotation errors
- **SOTA Performance**: Achieves state-of-the-art on MultiSpanQA and WikiText2 benchmarks in both English and Chinese; competitor models (XProvence v2, OpenSearch) fail on multilingual content or keyword-trap scenarios
- **Production-Ready**: 0.6B parameters (~600MB), MIT license, 8192-token context window, millisecond inference on CPU, deployable directly on search infrastructure

## Technical Details

### Architecture & Inference
The model uses an encoder architecture processing `[BOS] + Query + Context` concatenated input. Token-level scoring (0-1 range) aggregates to sentence-level scores via averaging. Pruning removes sentences below relevance thresholds, producing 70-80% token reduction without losing critical information. Single-pass parallel processing eliminates sequential decoding bottlenecks inherent to autoregressive approaches.

### Training Data Strategy
Built on MS MARCO, Natural Questions, GooAQ (English) and DuReader, Chinese Wikipedia, mmarco_chinese (Chinese). Crucial differentiator: annotations included LLM reasoning chains:
```json
{
  "query": "Who wrote The Killing of a Sacred Deer?",
  "think_process": "<think>The question asks about screenplay,
                    not original play author...</think>",
  "sentence_spans": [0, 1]
}
```
This self-verification reduces hallucination and enables debugging. Annotation via local Qwen3 8B deployment (cost-efficient alternative to API-based annotation).

### Benchmark Results
| Test Set | Our Model | XProvence v2 | Winner |
|-----------|-----------|--------------|--------|
| MultiSpanQA (EN) | SOTA | Lower | **Ours** |
| WikiText2 (EN) | SOTA | Lower | **Ours** |
| MultiSpanQA (ZH) | SOTA | N/A | **Ours** |
| WikiText2 (ZH) | SOTA | N/A | **Ours** |

Real-world case: Query "Who wrote The Killing of a Sacred Deer?" Our model correctly scores the screenwriter sentence (0.915) vs. distractor about original play (0.719). XProvence v1/v2 fail, scoring the distractor higher due to keyword attraction.

## Implications

**For RAG System Designers**: This model eliminates a fundamental cost-quality tradeoff. Previously, teams chose between costly full-context LLM processing or risky keyword-based filtering. Semantic highlighting enables cost-efficient pruning (70-80% savings) while improving reasoning quality by removing noise.

**Deployment Trade-offs**:
- Lightweight footprint (600MB) allows colocating with search infrastructure, reducing network latency and API costs
- CPU-capable inference enables offline processing of large document batches
- MIT license removes compliance friction vs. CC-BY-NC competitors

**When to Use**: Effective for any RAG application with long retrieved contexts and multi-sentence documents. Particularly valuable in agent loops requiring multiple reasoning steps—each with full context. Less critical for single-document QA with tight relevance scores.

**Alternatives Comparison**: XProvence models handle larger context windows but struggle with semantic nuance and multilingual content. Open Provence covers EN/JA but lacks Chinese support. OpenSearch operates on limited context (512 tokens) with proprietary licensing.

## Related

- [BGE-M3 Reranker v2](https://huggingface.co/BAAI/bge-m3-reranker) - Base model architecture
- [Zilliz Semantic Highlight Model](https://huggingface.co/zilliz/semantic-highlight-bilingual-v1) - Model weights and inference code
- [Token optimization in RAG systems](https://docs.llamaindex.ai) - Broader context on retrieval efficiency patterns
