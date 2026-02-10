# CRAFT: Continuous Reasoning and Agentic Feedback Tuning

| | |
|---|---|
| **Source** | HuggingFace Blog (FlyMyAI) |
| **URL** | [huggingface.co/blog/flymy-ai/craft-1](https://huggingface.co/blog/flymy-ai/craft-1) |
| **Researched** | 2026-02-07 |

## Overview

CRAFT is a model-agnostic agentic framework that wraps existing text-to-image generators with constraint verification and iterative refinement loops. Rather than retraining models, it decomposes natural language prompts into verifiable visual constraints, checks them against generated images using VLMs, and selectively refines only failing components. Performance improvements range from 8-13% on compositional prompts and 56.6 percentage points on preference scoring.

## Key Points

- **Constraint-based decomposition**: Converts prompts into structured yes/no visual questions, enabling systematic verification of each requirement independently
- **Verifiable outputs**: Uses VLM-based verification (DVQ metric) to quantify constraint satisfaction, providing explicit feedback for refinement decisions
- **Targeted regeneration**: Only refines prompt segments where verification fails, reducing unnecessary API calls and costs vs. naive retry loops
- **Model-agnostic stack**: Works with any combination of T2I generators (FLUX, Qwen, Z-Image), LLMs (GPT), and image editors (Gemini, Qwen), enabling custom budgets and vendor switching
- **~90 seconds latency**: Three iterations × ~30 seconds typical; iterative refinement converges quickly on compositional prompts

## Technical Details

**Pipeline**: Decompose → Generate → Verify → Refine (targeted edits only) → Iterate

**Performance benchmarks** (DSG-1K compositional dataset):
- FLUX-Schnell: 0.78 → 0.86 VQA (10% absolute improvement)
- FLUX-Dev: 0.77 → 0.87 (13% absolute)
- Qwen-Image: 0.864 → 0.946 (9% absolute)
- Auto SxS preference: 19% → 75.6% for FLUX-Schnell (dramatic preference win)

Particularly effective for complex text rendering and multi-element studio compositions where single-pass generation typically fails. VLM verification acts as a differentiable feedback signal for prompt refinement.

## Implications

**Strengths for agentic patterns**: CRAFT demonstrates the architectural value of explicit constraint satisfaction loops over black-box retraining. No model changes required—plugs into existing inference pipelines. Verifiable outputs enable deterministic monitoring and failure attribution.

**Trade-offs**: Latency compounds with iteration count (90s for three rounds). API costs multiply (N LLM/VLM calls per image). VLM verification errors propagate through refinement loops. Abstract or artistic prompts resist decomposition into hard constraints.

**When to adopt**: High-stakes visual generation (marketing, product) where constraint verification outweighs latency; cost-sensitive applications where targeted refinement beats naive retries; systems requiring output provenance and explicit quality metrics.

**Architectural patterns**: This is a template for agentic feedback loops beyond image generation—applicable to code synthesis (test-driven refinement), data validation, and multi-step reasoning where intermediate verification drives refinement strategy.

## Sources

- [CRAFT on HuggingFace Blog](https://huggingface.co/blog/flymy-ai/craft-1) - Official framework overview
- [CRAFT Demo](https://craft-demo.flymy.ai/) - Interactive demonstration
- [CRAFT Research](https://research.flymy.ai/craft/) - Technical details
- [arxiv.org/abs/2512.20362](https://arxiv.org/abs/2512.20362) - Full paper with benchmarks
