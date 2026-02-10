# CRAFT: Continuous Reasoning and Agentic Feedback Tuning

| | |
|---|---|
| **Source** | Hugging Face Blog (Flymy AI) |
| **URL** | [huggingface.co/blog/flymy-ai/craft-1](https://huggingface.co/blog/flymy-ai/craft-1) |
| **Researched** | 2026-02-05 |

## Overview

CRAFT is a model-agnostic wrapper that improves text-to-image generation by adding explicit reasoning and iterative refinement without retraining. It decomposes prompts into verifiable visual constraints, checks them with a vision-language model, and iteratively refines only failing constraints—achieving 8-9 percentage point improvements across compositional and long-form prompts.

## Key Points

- **Zero retraining required**: Plug-and-play wrapper works with any T2I model (FLUX, Qwen, Stable Diffusion variants) and any VLM
- **Constraint-driven iteration**: Converts natural language prompts into structured yes/no questions, enabling targeted refinement rather than full prompt rewrites
- **Dual pathways**: Supports both generation and image editing modes, switching automatically when generation fails
- **Significant gains on compositional tasks**: Win rates improve from 21-36% to 59-74% against baseline; VQA scores +8-9 percentage points
- **Modest computational overhead**: ~30 seconds per iteration with typical convergence in 3 rounds

## Technical Details

CRAFT operates as a five-step feedback loop:

1. **Decompose** - LLM converts prompt into structured constraints (e.g., "Is there a cobra?" "Is it salad-textured?")
2. **Generate** - Existing T2I model produces image
3. **Verify** - VLM (ChatGPT nano or Gemini Flash) answers constraint questions
4. **Refine** - LLM updates only failing constraint portions of prompt
5. **Iterate** - Loop until constraints pass or budget exhausted

The model-agnostic design means it adapts to underlying T2I quality—tested on FLUX-Schnell, FLUX-Dev, FLUX-2 Pro, and Qwen-Image, all show consistent improvements. Early stopping prevents excessive iteration costs.

Limitations: VLM judgment errors propagate through the loop; abstract or poetic prompts don't decompose cleanly to binary constraints; API costs accumulate with iteration (though reasonable at ~3 rounds typical).

## Implications

**Architectural significance**: This represents a shift from end-to-end finetuning toward agentic composition—combining off-the-shelf components (T2I, VLM, LLM) into a reasoning system. For practitioners building production T2I systems, CRAFT offers immediate ROI: improve quality on existing models without retraining infrastructure.

**When to apply**: Compositional accuracy and text rendering are your bottlenecks. Cost-benefit is strongest when you need human-parity on complex spatial relationships or text overlays (product mockups, UI designs, infographics). Less valuable for abstract/artistic prompts that resist constraint decomposition.

**Design patterns**: This exemplifies the broader trend of LLM-as-judge, iterative refinement loops, and decoupling quality improvement from model training. Consider similar patterns for code generation, document retrieval, and other structured output tasks where external verification is tractable.

## Sources

- [CRAFT GitHub/Paper](https://arxiv.org/abs/2512.20362) - Full technical paper with extended results
- [CRAFT Live Demo](https://craft-demo.flymy.ai/) - Interactive interface to test on your prompts
