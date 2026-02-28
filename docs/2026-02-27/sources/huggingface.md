# Hugging Face Blog

| | |
|---|---|
| **URL** | [huggingface.co/blog](https://huggingface.co/blog) |
| **Scanned** | 2026-02-27 |
| **Since** | 2026-02-23 |
| **Articles** | 4 |
| **Deduplicated** | Yes |
| **Removed** | 2 |

---

## Mixture of Experts (MoEs) in Transformers
- **URL:** https://huggingface.co/blog/moe-transformers
- **Published:** 2026-02-26
- **Description:** Explores how Mixture of Experts are integrated into Hugging Face Transformers as first-class citizens. The article covers weight loading optimization (~3x speedup), expert backend execution architectures, expert parallelism distribution, and collaboration with Unsloth enabling ~12x faster MoE training with >35% VRAM reduction.
- **Relevance:** Directly relevant to LLM optimization, transformer research, and open-source model development. Demonstrates advanced training techniques for large-scale models.

## Your MoE Model Does Not Have to Select Fixed Number of Experts
- **URL:** https://huggingface.co/blog/Spico/dynamic-routing
- **Published:** 2026-02-26
- **Description:** Introduces dynamic routing techniques for Mixture-of-Experts models that adaptively select the optimal number of experts for each token, addressing inefficiency of fixed top-k routing. Covers three main approaches: thresholding, dynamic proposer, and zero-computation experts.
- **Relevance:** Highly relevant to LLM optimization and transformer research. Presents novel approaches to expert selection and computational efficiency in large models.

## A framework and leaderboard for Retrieval Pipelines evaluation on ViDoRe v3
- **URL:** https://huggingface.co/blog/antoineedy/vidore-v3-pipeline-framework-and-leaderboard
- **Published:** 2026-02-27
- **Description:** Introduces the ViDoRe v3 Pipeline evaluation framework and leaderboard for evaluating retrieval pipelines on visual document retrieval tasks. Addresses how modern industrial retrieval systems combine multiple components including OCR, VLMs, sparse/dense search, re-ranking, and retrieval agents with self-correction.
- **Relevance:** Relevant to RAG systems, LLM-grounded retrieval, and AI tools for document understanding. Addresses modern retrieval agent architectures with self-correction capabilities.

## Do Bubbles Form When Tens of Thousands of AIs Simulate Capitalism?
- **URL:** https://huggingface.co/blog/FINAL-Bench/pumpdump
- **Published:** 2026-02-24
- **Description:** Large-scale experiment with tens of thousands of AI agents equipped with metacognitive capabilities competing in a simulated capitalist trading environment. Reveals that individual agent rationality does not guarantee collective rationality, with key findings on bubble formation, herding behavior, and information asymmetry effects in multi-agent systems.
- **Relevance:** Relevant to agentic patterns and multi-agent systems research. Demonstrates emergent behavior in large-scale AI agent simulations and provides insights into collective AI behavior and safety considerations.
