# Embedding Robustness: Axiom Core's Invariant Layer

| | |
|---|---|
| **Source** | GitHub - Architect-Flow78/axiom-core |
| **URL** | [github.com/Architect-Flow78/axiom-core](https://github.com/Architect-Flow78/axiom-core) |
| **Researched** | 2026-01-28 |

## Overview

AXIOM Core implements a learned neural wrapper layer that transforms standard embeddings into invariant semantic fingerprints, preserving meaning across paraphrasing, word reordering, typos, light prompt injection, and noise. The system uses contrastive learning with InfoNCE loss to train binarized hash representations that remain stable under perturbations—addressing a critical gap in embedding robustness for adversarial defense scenarios.

## Key Points

- **Wrapper architecture**: AXIOM sits between embeddings and downstream tasks, enabling plug-and-play integration with existing embedding models without replacement
- **Invariant gate mechanism**: Two-stage processing (encoder + constrained gate via Tanh) reduces dimensionality and creates bounded representations suitable for binarization
- **Binary hashing**: `torch.sign()` converts continuous outputs to discrete signatures (-1, 0, 1), enabling fast comparisons and robustness properties
- **Contrastive training**: InfoNCE loss on augmented pairs (positive samples, in-batch negatives) forces semantic clustering despite transformations
- **Early-stage validation**: Repository includes noise testing (~0.02 magnitude Gaussian perturbations) and contrastive training loops (800 iterations), but production performance benchmarks unavailable

## Technical Details

**Architecture:**
```
Input Embedding → Encoder (Linear→LayerNorm→GELU)
                → Invariant Gate (Linear→LayerNorm→Tanh)
                → Optional binarization via torch.sign()
```

**Default dimensions**: d_model=384, hidden=256, inv_dim=128

**Training approach:**
- Loss function: InfoNCE with temperature=0.1
- Positive pairs: augmented views of same sample
- Negative pairs: all other samples in batch
- Perturbation magnitudes: 0.02 during training, 0.01 for evaluation
- Metric: cosine similarity between original and perturbed embeddings

**Robustness targets:**
- Paraphrasing
- Word reordering
- Typos
- Light prompt injection
- Gaussian noise

## Implications

**Architectural trade-offs:**
- Binarization compresses information (384→128 dimensions typically) but enables fast hashing and discrete semantic fingerprints; measure actual information loss in your domain
- Tanh gating constrains outputs to [-1,1] but reduces effective capacity compared to unbounded transformations
- Contrastive loss assumes semantic invariance is learnable from augmentation alone; may underperform on domain-specific adversarial patterns

**When to consider AXIOM:**
- You need embedding robustness for safety-critical classification or retrieval tasks where prompt injection or typos degrade performance
- Your embedding model produces high-dimensional vectors where binarization is acceptable
- You want minimal changes to existing inference pipelines (wrapper pattern is non-invasive)

**What's missing for production:**
- Comparative benchmarks against adversarial robustness baselines (certified defenses, randomized smoothing, etc.)
- Quantitative metrics on real-world adversarial datasets (GLUE, adversarial NLI, etc.)
- Computational overhead analysis (forward pass latency, memory for storing binarized representations)
- Ablation studies on dimensionality reduction impact

**Alternatives to evaluate:**
- Certified robustness approaches using randomized smoothing (guaranteed bounds but higher computational cost)
- Adversarial training on raw embeddings (task-specific but may not generalize)
- Ensemble methods combining multiple embedding spaces

## Related

- [GitHub - Architect-Flow78/axiom-core](https://github.com/Architect-Flow78/axiom-core) - Original repository with implementation
- [ArXiv: Learning to Play Games in Minutes with Expanding Object-Centric Models](https://arxiv.org/abs/2505.24784) - Related AXIOM work on invariant structure learning (distinct project)
- [GitHub - buk81/uniformity-asymmetry](https://github.com/buk81/uniformity-asymmetry) - Related work on calibrated detection in LLM embeddings
