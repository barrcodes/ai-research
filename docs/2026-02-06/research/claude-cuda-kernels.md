# We Got Claude to Build CUDA Kernels and Teach Open Models

| | |
|---|---|
| **Source** | Hugging Face Blog |
| **URL** | [huggingface.co/blog/upskill](https://huggingface.co/blog/upskill) |
| **Researched** | 2026-02-06 |

## Overview

Hugging Face introduced **upskill**, a framework for knowledge distillation that captures Claude Opus 4.5's reasoning as portable "skills"—reusable prompts and task specifications that enable smaller models to solve domain-specific problems. The proof-of-concept demonstrates Claude successfully generating complete CUDA kernels with proper architecture targeting and PyTorch bindings, then teaching this capability to cheaper models with +35-45% accuracy improvements.

## Key Points

- **Skill-based knowledge transfer**: Domain expertise encoded as ~500-token skills that travel across Claude Code, Cursor, and other AI tools, eliminating vendor lock-in
- **Validated performance**: Claude Opus 4.5 generated production-quality CUDA kernels with H100-specific optimizations; skills maintained equivalent performance in downstream models
- **Model-specific ROI**: Open-source and smaller models (Haiku, Sonnet) saw significant accuracy gains, but benefits vary by model architecture—requires evaluation before deployment
- **Cost optimization trade-off**: Skills enable using cheaper models for specialized tasks, but don't uniformly improve frontier models (Opus showed increased token usage without accuracy gains)

## Technical Details

Claude Opus 4.5 successfully generated complete CUDA kernels including:
- Project structure with `build.toml` configuration
- Architecture-specific targeting (H100 compute capability 9.0)
- Shared memory alignment (128-byte boundaries)
- Async memory operations with version guards (`__CUDA_ARCH__ >= 900`)
- PyTorch C++ bindings

The three-phase workflow:
1. Teacher generates: Claude Opus 4.5 builds interactively and exports execution trace
2. Skill creation: Convert trace into SKILL.md + test cases following Agent Skills specification
3. Student transfer: Deploy skill to target models and evaluate accuracy improvement

Example baseline improvement showed skills lifting accuracy from 60% → 95% on kernel generation tasks (+35%), with skill token costs around 520 tokens per deployment.

## Implications

**For practitioners:** This validates LLM-as-teacher architectures for specialized domains. Rather than fine-tuning expensive models or retraining smaller ones, you can now capture frontier model reasoning into cheap-to-deploy skills. The mechanism is framework-agnostic (Agent Skills spec), so skills trained on Claude can port to other tools.

**Architectural consideration:** Skills create a new abstraction layer between capability and model. This enables cost-optimization strategies—use Haiku + skills instead of Opus for constrained deployments—but requires benchmarking before deployment since not all models benefit equally.

**For infrastructure teams:** This pattern suggests a future where specialized tasks (CUDA optimization, complex algorithms, domain DSLs) are captured once by frontier models, then cached as lightweight skill artifacts rather than constantly re-invoking expensive inference. Shifts the cost model from per-query to per-skill-generation.

## Sources

- [Hugging Face Blog: upskill](https://huggingface.co/blog/upskill) - Framework for LLM knowledge transfer through portable agent skills
