# PlaceboBench: Kimi K2.5 vs Claude Opus 4.6 on Pharmaceutical Hallucinations

| | |
|---|---|
| **Source** | Blue Guardrails / Reddit |
| **URL** | [blueguardrails.com/en/blog/placebo-bench-an-llm-hallucination-benchmark-for-pharma](https://www.blueguardrails.com/en/blog/placebo-bench-an-llm-hallucination-benchmark-for-pharma) |
| **Researched** | 2026-02-20 |

## Overview

PlaceboBench is a novel domain-specific hallucination benchmark for pharmaceutical RAG systems using real clinical questions from the SVELIC database and official EMA product information documents. Results show Kimi K2.5 achieves 26.1% hallucination rate compared to Claude Opus 4.6's 63.8%—a significant gap that contradicts Opus's strong performance on generic benchmarks, highlighting the critical importance of domain-specific evaluation for production model selection.

## Key Points

- **Benchmark design addresses fundamental flaws**: Existing hallucination benchmarks (RAGTruth, FaithBench) rely on stale data (CNN/Daily Mail 2016, MSMarco 2016) with high risk of training data leakage, use synthetic failure scenarios, and lack domain specificity. PlaceboBench uses novel, realistic pharmaceutical tasks with no synthetic data injection.

- **Test dataset composition**: 69 real clinical questions from Swedish/Norwegian drug information centers spanning 23 drugs; ground truth from EMA's 2,156 drug product information documents (Summary of Product Characteristics and patient leaflets). Median SmPC length: 42 pages.

- **Measured hallucination rates (percentage of responses containing ≥1 hallucinated claim)**:
  - Gemini 3 Pro: 26.1% (lowest)
  - Kimi K2.5: 34.8%
  - Gemini 3 Flash: 39.1%
  - Claude Sonnet 4.5: 39.5%
  - GPT 5.2: 40.6%
  - GPT 5 Mini: 46.4%
  - Claude Opus 4.6: 63.8% (highest)

- **Hallucination type distribution**: Fabrication is most frequent across all models, followed by world knowledge insertion—models contradict explicit instructions to use only retrieved context and instead inject pretraining knowledge.

- **Cost-performance tradeoff**: Opus 4.6 is most expensive ($92.82/1,000 prompts) with highest hallucination rate. Gemini 3 Pro achieves lowest hallucination rate at lowest cost among commercial flagships. Kimi K2.5 (serverless) provides competitive performance at lower cost tier.

- **Reasoning efficiency paradox**: Opus 4.6 produces only 353 median thinking tokens despite highest hallucination rate; GPT 5 Mini generates 3,037 thinking tokens. This inverse relationship suggests Opus's efficiency may correlate with insufficient reasoning depth for domain-specific grounding.

## Technical Details

**Methodology**:
- PDF extraction from EMA documents using standard text conversion tools
- Document chunking: 300-word chunks with 30-word overlap, yielding 131,467 total chunks
- Embedding: Google's embedding-gemma-300m (768-dimensional), stored in Qdrant vector database
- Retrieval pipeline: top-k=10 semantic search with 1-chunk window expansion for context
- Median prompt size: 13,668 input tokens (~10-20 pages of retrieval context per query)
- System prompt explicitly instructs: answer strictly from documents, no external knowledge, 1-2 paragraph responses, use precise medical terminology

**Annotation framework** (8-category claim-level analysis):
1. Fabrication (invented claims)
2. Context misattribution
3. Reasoning error
4. Incorrect refusal
5. Omission
6. Terminological imprecision
7. World knowledge (pretraining injection)
8. Contradiction

Quality control: Double-annotation process with second reviewer, 10-20 minutes per response for manual verification. Automated hallucination detection using RLM-inspired agentic verification approach with F1-scores 0.85-0.95 on character overlap of annotated spans.

**Critical observation**: Hallucinations are cognitively difficult to detect. Reviewed responses appear fluent, coherent, and well-grounded despite containing false claims. Deep analysis or SME expertise required to identify failures.

## Implications

**For practitioners building pharmaceutical RAG systems**:

1. **Generic benchmarks don't predict domain performance**: Opus 4.6's top-tier performance on coding/reasoning leaderboards doesn't transfer to pharmaceutical grounding. Domain-specific evaluation is non-negotiable for safety-critical applications.

2. **Hallucination acceptability varies by risk profile**: Some world knowledge injection (abbreviation expansion, technical explanation) may be contextually valuable, but pharmaceutical teams have strict prohibition against inferences not directly grounded in regulatory documents. Individual risk assessment required.

3. **Specific failure patterns in pharma domain**:
   - Side effect frequency confusion (rare vs. common) with direct clinical decision impact
   - Dose/dosing scheme generalization from specific indications to all conditions
   - Clinical protocol invention for monitoring—not directly supported by SmPC but plausibly inferred

4. **Model selection requires trade-off analysis**:
   - Lowest hallucination (Gemini 3 Pro) requires API dependency on Google
   - Cost efficiency vs. hallucination rate doesn't correlate linearly
   - Reasoning token budget inversely relates to hallucination in Opus 4.6, suggesting optimization toward response latency over grounding

5. **Architectural implications**:
   - Retrieval quality alone insufficient (10-20 pages of context retrieved, models still hallucinate)
   - Reasoning tokens alone insufficient predictor of fidelity
   - Claim-level annotation framework enables fine-grained error analysis beyond binary pass/fail
   - RLM-inspired verification approaches show promise (F1 0.85-0.95) for automated detection, but require domain-specific recalibration for new data/domains

6. **Production deployment considerations**:
   - All tested models exceed acceptable hallucination rates (26-64%) for unfiltered deployment in regulated pharmaceutical contexts
   - Downstream verification step mandatory—model output cannot be trusted without human SME review
   - Omission rates likely higher in production (benchmark only measured omissions in retrieved context; retrieval failures not evaluated)

## Sources

- [Blue Guardrails PlaceboBench Article](https://www.blueguardrails.com/en/blog/placebo-bench-an-llm-hallucination-benchmark-for-pharma) - Full methodology, results, dataset description, and analysis
- [Reddit r/LocalLLaMA Post](https://old.reddit.com/r/LocalLLaMA/comments/1r9tdvr/kimi_k25_better_than_opus_46_on_hallucination/) - Community discussion and context from aiprod, benchmark creator
