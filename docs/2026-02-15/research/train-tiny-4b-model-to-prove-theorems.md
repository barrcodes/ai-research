# Training Small Models for Theorem Proving

| | |
|---|---|
| **Source** | Reddit Discussion (MachineLearning) + Academic Literature |
| **URL** | [Multiple sources - see Sources section] |
| **Researched** | 2026-02-15 |

## Overview

Training small language models (4B-7B parameters) to prove formal theorems is empirically feasible through synthetic data generation, proof-state exploration, and iterative refinement. Recent breakthroughs demonstrate that compact models can achieve comparable performance on mathematical benchmarks (miniF2F, ProofNet) to much larger models when trained on high-quality synthetic traces and provided a structured exploration mechanism like HyperTree Proof Search or Monte Carlo tree search variants.

## Key Points

- **Synthetic Data is Critical**: One-shot fine-tuning on high-quality synthetic proof traces allows 7B models to saturate Pass@1 rates on standard benchmarks that previously required hundreds of millions of human-annotated examples
- **State Space Exploration Over Generation**: Moving from single-step proof generation to step-by-step state exploration (treating proof states as graph nodes) dramatically improves success rates on hard problems
- **Dual-Agent Training**: Co-training a conjecturer (generates new problems) and prover (solves them) creates a self-improving feedback loop without requiring human problem generation
- **Verification-Driven Learning**: Iterative proof verification and correction with LLM modules specializing in synthesis vs. correction significantly outperforms monolithic models
- **Modest Scale Matters**: Even 7B-parameter models can achieve 67% accuracy on miniF2F benchmarks when properly trained—a 20% improvement over previous SOTA

## Technical Details

### Proof State as Graph Search

Meta AI's HyperTree Proof Search (HTPS) treats theorem proving as graph exploration:
- Each incomplete proof state = node
- Each proof tactic application = edge
- Uses Monte Carlo tree search principles adapted for proof search:
  1. Prior estimation: Which tactics are reasonable from this state?
  2. Outcome evaluation: Will this path lead to proof completion?
  3. Progressive refinement: Graph structure improves with iterations

This contrasts with earlier language-model-only approaches that attempt proof generation in a single forward pass, which cannot adapt when hitting dead ends.

### Training Data Strategy

**Synthetic vs. Human Data:**
- Synthetic traces generated from formal proof assistants (Lean, Isabelle, Coq) are sufficient for training
- High-quality is more important than volume—concentrated on tactic sequences in standard libraries (Lean's Mathlib)
- Dual-agent systems (conjecturer/prover) generate unlimited curriculum of problems slightly beyond current capability

**Online Learning Refinement:**
- Pre-train on formal proof corpus (general mathematical knowledge)
- Online fine-tune on specific problem families (e.g., IMO-like problems)
- This two-phase approach enables rapid adaptation without expensive full retraining

### Model Architecture Considerations

- **Proof synthesis module**: Generates candidate tactics/lemmas
- **Proof verification module**: Evaluates partial proofs for correctness
- **Modular tactic libraries**: Breaking proofs into reusable lemma blocks improves generalization
- **Context length**: DeepSeek Prover-V2 (7B variant) uses 32K token context—essential for complex multi-step proofs

## Implications

### For ML Practitioners

1. **Scale is not destiny**: Small models solve hard problems when given proper exploration mechanisms. The gap between 4B and 7B models vs. larger systems narrows dramatically with structured search.

2. **Data efficiency is achievable**: Synthetic data from formal systems eliminates human bottleneck. This is architecturally different from NLP scaling laws where human data is required.

3. **Verification-aware training wins**: Unlike open-ended generation, theorem proving benefits from tight coupling with an oracle (formal proof checker). Design training loops around this constraint.

4. **Search algorithm matters more than raw LLM capability**: A weak LLM with MCTS-style exploration outperforms a stronger LLM doing greedy generation. Invest in search algorithms, not just model capacity.

### For Formal Methods

- **Automating routine proofs**: Lean/Coq developers can now automate significant portions of routine proof development
- **Proof by exhaustive case**: Complex proofs involving case analysis (like IMO arithmetic problems) are becoming tractable for small models
- **Integration with development**: VSCode plugins enable real-time proof suggestions during development

### Architectural Trade-offs

| Approach | Strengths | Weaknesses |
|----------|-----------|-----------|
| **Large generative LLM** | Broad knowledge, single pass | Can't recover from dead ends, context limit issues |
| **Small LLM + graph search** | Efficient, adaptive, verifiable | Requires formal system integration, slower inference |
| **Dual-agent (conjecturer/prover)** | Self-improving, unlimited curriculum | Training complexity, computational cost of generation loop |

## Sources

- [Meta AI: Teaching AI Advanced Mathematical Reasoning](https://ai.meta.com/blog/ai-math-theorem-proving/) - HyperTree Proof Search beating IMO benchmarks with neural approaches
- [arXiv: A Survey on Deep Learning for Theorem Proving](https://arxiv.org/html/2404.09939v2) - Comprehensive overview of DL4TP methods
- [OpenReview: LeanAgent - Lifelong Learning for Formal Theorem Proving](https://openreview.net/forum?id=Uo4EHT4ZZ8) - Iterative learning in proof assistants
- [DeepSeek Prover-V2 Release](https://www.infoq.com/news/2025/05/deepseek-prover-v2-formal-proof/) - 7B and 671B models for formal mathematics
- [arXiv: LeanProgress - Guiding Search for Neural Theorem Proving via Proof Progress Prediction](https://arxiv.org/html/2502.17925v3) - State-of-art search guidance
- [GitHub: DL4TP Survey](https://github.com/zhaoyu-li/DL4TP) - Curated resources and benchmarks
- [NeurIPS 2023 Tutorial: Machine Learning for Theorem Proving](https://machine-learning-for-theorem-proving.github.io/img/NeurIPS2023-Tutorial-ML4TP.pdf) - Educational overview
