# Fine-Tuning GPT-5 for GPU Kernel Generation

| | |
|---|---|
| **Source** | arXiv (2602.11000) |
| **URL** | [arxiv.org/html/2602.11000](https://arxiv.org/html/2602.11000) |
| **Researched** | 2026-02-13 |

## Overview

Researchers demonstrate that reinforcement learning from verifiable rewards (RLVR) significantly improves LLM performance on GPU kernel generation. A fine-tuned GPT-5 model increases functional correctness from 43.7% to 77.0% and achieves a 2.12x geometric mean speedup over PyTorch's TorchInductor when deployed in a full coding agent. The approach replaces traditional supervised fine-tuning with execution-based feedback, addressing the data scarcity problem inherent in GPU programming tasks.

## Key Points

- **RLVR Methodology**: Uses deterministic, rule-based reward functions based on compilation success, functional correctness, and performance metrics rather than labeled training pairs.
- **Correctness Gains**: Single-attempt correctness improves 33.3 percentage points (43.7% → 77.0%); reaches 83.7% with three refinement attempts.
- **Performance Advantage**: 21.8% of generated kernels outperform TorchInductor (vs 14.8% baseline); geometric mean speedup of 2.12x in full agent deployment.
- **Dataset Curation**: Trained on curated dataset of 11,363 problems spanning matrix multiplication, convolution, reduction, and fusion patterns derived from PyTorch repositories and KernelBench validation set.
- **Scale Matters**: Incremental dataset sizes (100, 1K, 11.3K problems) show consistent improvements, with larger datasets yielding better generalization.

## Technical Details

The approach reformulates kernel generation as an RL problem where models learn from execution feedback rather than static labeled examples. Training data came from two sources: public PyTorch repositories and the KernelBench validation set (264 problems). Extensive curation involved deduplication, difficulty ranking, shape diversification, and runtime filtering to create diverse, representative training distributions.

**Evaluation Against Baselines:**
- Base GPT-5: 43.7% correctness
- Claude Opus 4.5, Gemini 2.5 Pro, Grok: all outperformed
- MakoraGenerate (multi-agent evolutionary system): downstream performance beats single-agent approaches

Per-difficulty analysis shows performance varies by task complexity, with harder operations (reductions, fusions) presenting greater challenges. The geometric mean speedup metric prevents outliers from inflating results—critical for production kernel selection.

## Implications

This work signals a fundamental shift: execution-based RL is more effective than supervised fine-tuning for code generation tasks with verifiable outputs. Key implications for practitioners:

1. **Data Strategy**: For kernel optimization, collect execution traces and performance metrics rather than hunting for labeled examples. This scales the training pipeline.

2. **Deployment Pattern**: Full coding agents outperform single-pass generation (97.4% problem resolution in expanded KernelBench). Integrate refinement loops that treat LLMs as iterative optimizers, not one-shot generators.

3. **Benchmark Selection**: KernelBench's 250 well-defined tasks provide meaningful evaluation. Direct PyTorch compiler comparison is essential—synthetic metrics don't capture real-world speedups.

4. **Trade-offs**: 21.8% of kernels exceed TorchInductor—not majority. Reserve LLM-generated kernels for bottleneck regions where 2.12x speedup justifies verification overhead. For general cases, compiler defaults remain safer.

5. **Model Sensitivity**: Results specific to GPT-5 and trained model. Deployment requires evaluating against your target hardware (NVIDIA H100, AMD MI300, etc.) and compiler baselines.

## Sources

- [Fine-Tuning GPT-5 for GPU Kernel Generation](https://arxiv.org/html/2602.11000) - Main research paper with RLVR methodology and performance results
- [arXiv abstract](https://arxiv.org/abs/2602.11000) - Citation and metadata
- [KernelBench](https://scalingintelligence.stanford.edu/blogs/kernelbench/) - Benchmark suite for LLM kernel generation evaluation
- [GPU Kernel Scientist](https://arxiv.org/html/2506.20807v1) - Related work on iterative kernel optimization framework
