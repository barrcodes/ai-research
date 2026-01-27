---
title: OptiMind - Research model designed for optimization
source: Microsoft Research / Hugging Face
url: https://huggingface.co/blog/microsoft/optimind
researched_at: 2026-01-26T00:00:00Z
---

# OptiMind: A 20B Specialized Model for Optimization

## Overview

OptiMind is a compact 20-billion parameter language model trained to translate natural language problem descriptions directly into solver-ready mathematical formulations. Rather than competing on general intelligence, it targets the specific bottleneck in optimization workflows: the effort to formulate problems into mathematical form that solvers can execute.

## Key Points

- **Model Size**: 20B parameters with 3.6B activated per forward pass (MoE variant), using context window of 128K tokens
- **Accuracy Improvement**: 20.7% formulation accuracy gain over base model; outperforms all open-source models under 32B parameters
- **Output Format**: Generates both mathematical formulations and executable GurobiPy code
- **Architecture**: Mixture-of-Experts transformer variant optimized for expert reasoning
- **Licensing**: MIT-licensed, available on Hugging Face and Microsoft Foundry

## Technical Details

| Aspect | Specification |
|--------|---------------|
| Parameters | 20B total, 3.6B activated |
| Training Hardware | 8x NVIDIA B200 GPUs |
| Inference Hardware | 8x H100 / A100+ (32GB+ VRAM) |
| Context Length | 128,000 tokens |
| Output Format | Mathematical model + GurobiPy |
| License | MIT |

OptiMind uses specialized training on optimization domains (supply chain, manufacturing, logistics, portfolio optimization) rather than general instruction-following. The MoE architecture allows it to maintain competitive performance at smaller scale—3.6B activated parameters match larger dense models.

## Implications

**When OptiMind Solves Real Problems**: Use it when problem *formulation* (converting business constraints to mathematical form) is your bottleneck, not solver performance. This applies to domains like workforce scheduling, logistics network design, and financial portfolio optimization where human experts spend weeks manually building models.

**When It Doesn't Fit**: If your optimization problem is already well-structured (mathematical form known) or requires solving speed matters more than formulation ease, standard solvers with familiar problem formats remain preferable.

**Practical Trade-offs**: The 20B size requires 32GB+ GPU memory for inference—no CPU inference. Formulation accuracy remains 79-90% depending on problem complexity, meaning human review of generated code is essential before solver execution. The GurobiPy output is opinionated and may require adjustment for non-standard constraints.

**Integration Path**: Load from Hugging Face or Microsoft Foundry, pipe problem descriptions through the model, validate the generated formulation against your constraint set, then execute with your existing solver infrastructure.

## Related

- [Microsoft Research Blog - OptiMind Technical Details](https://www.microsoft.com/en-us/research/blog/optimind-a-small-language-model-with-optimization-expertise/) - Architecture, training methodology, and benchmark details
- [ArXiv Paper - OptiMind: Teaching LLMs to Think Like Optimization Experts](https://arxiv.org/html/2509.22979v1) - Full methodology and evaluation
- [Hugging Face Model Card - OptiMind-SFT](https://huggingface.co/microsoft/OptiMind-SFT) - Model weights and inference examples
