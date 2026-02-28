# Reproducing Google's Nested Learning / HOPE in PyTorch

| | |
|---|---|
| **Source** | Reddit r/MachineLearning |
| **URL** | [old.reddit.com/r/MachineLearning/comments/1res42m/](https://old.reddit.com/r/MachineLearning/comments/1res42m/p_reproducing_googles_nested_learning_hope_in/) |
| **Researched** | 2026-02-27 |

## Overview

Google's Nested Learning paper introduces a fundamentally different paradigm for training neural networks that treats models as nested, multi-level optimization problems rather than monolithic architectures. The HOPE (self-modifying sequence model combined with Continuum Memory System) architecture enables continual learning and handles extremely long contexts. A community member (kmccleary3301) has created a mechanism-faithful PyTorch reproduction that achieves 600+ stars and is now available on PyPI, enabling researchers to experiment locally despite Google not releasing official code.

## Key Points

- **Nested Learning Paradigm**: Models are viewed as interconnected multi-level optimization problems, each with its own update rate and context flow. The model architecture and optimization algorithm are different levels of the same concept rather than separate concerns.

- **HOPE Architecture**: Combines a self-modifying sequence model with a Continuum Memory System (CMS). HOPE is self-referential—it optimizes its own memory through a recursive process and can theoretically support unbounded levels of in-context learning without architectural changes.

- **Continuum Memory System**: Extends traditional memory (LSTM/GRU) into a spectrum of memory banks, each updating at different frequencies. Faster-updating banks handle immediate information; slower ones consolidate abstract knowledge over longer periods. This creates natural multi-scale temporal learning.

- **PyTorch Reproduction Status**: Mechanism-level implementation is faithful to the paper; full paper-scale compute-parity is not claimed. Available as `nested-learning` on PyPI with new CLI tools (`nl doctor`, `nl smoke`, `nl audit`, `nl train`) for reproducible local workflows.

- **Performance vs. Standard Models**: On CLINC, Banking, DBpedia (class-incremental), HOPE outperforms in-context learning, Elastic Weight Consolidation, and external learning methods when augmenting Llama3. On RULER and BABILong benchmarks, HOPE surpasses recurrent and attention-free architectures, especially for recall/manipulation tasks over 100k+ token contexts.

## Technical Details

### Architecture Components

The HOPE implementation centers on three core mechanisms:

1. **Self-Modifying Blocks**: The model learns its own update algorithm, represented as differentiable operations on its hidden state. This is distinct from typical architecture—the update rule itself becomes a learned module that can change based on context.

2. **Continuum Memory System**: Unlike discrete memory banks, CMS creates a continuous spectrum where memory operations occur at different timescales. Each "bank" has its own update frequency, creating a form of temporal hierarchy without explicit separation.

3. **Gradient-as-Memory**: The paper reframes standard optimizers (Adam, SGD with momentum) as associative memory modules that compress gradient information. HOPE uses more expressive optimizers with deeper memory and powerful learning rules.

### Implementation Choices

- **Optimizer Integration**: Muon optimizer for 2D+ tensors; AdamW for embeddings/norms. This asymmetric choice reflects different convergence properties needed for different parameter groups.
- **Acceleration**: torch.compile for attention and core loops with bf16 autocast mixed precision. Falls back to eager execution if needed.
- **Dependency Management**: Uses `uv` rather than traditional pip, enabling reproducible pinned environments.

### What Differs from Google's Work

The reproduction achieves mechanism-level faithfulness but does not claim full-scale parity with Google's internally-trained models. Key constraints:

- Local compute resources limit full-scale continual learning experiments (multi-corpus sequential learning)
- Evaluation on long-context tasks (100k+ tokens) would require significant infrastructure
- The reproduction focuses on architectural correctness and reproducibility rather than pushing state-of-the-art results

## Implications

### For Training Efficiency

Nested Learning attacks continual learning from a fundamentally different angle than current approaches (replay buffers, EWC, adapter modules). By encoding learning at multiple timescales into the architecture itself rather than post-hoc, it avoids catastrophic forgetting without external memory overhead. This is particularly valuable for deployment scenarios where you cannot store and replay historical data.

### For Long-Context Reasoning

The performance on RULER (50k+ token contexts) and BABILong (up to 1M tokens) suggests that Nested Learning's multi-timescale memory is more efficient than standard transformer attention for maintaining coherent state over extreme context windows. This has immediate implications for RAG systems and long-document analysis where current models degrade.

### For Architecture Design

The reframing of optimizers as memory modules is conceptually significant: it suggests that the boundary between "optimizer" and "architecture" is artificial. Gradient history is as much a part of the model's representational capacity as hidden states. This could reshape how we think about backprop-free models or hybrid learning schemes.

### Accessibility and Reproduction

The availability of a polished, pip-installable reproduction with documentation removes a significant barrier to experimentation. Unlike papers where authors don't release code (common at Google), the community reproduction enables immediate research follow-ups. However, the disclaimer about not achieving full-scale parity is important—researchers attempting to verify the full claims will still face substantial compute requirements.

### Remaining Questions

- **Computational Overhead**: Does the self-modifying mechanism impose significant runtime cost compared to standard transformers? The paper doesn't provide detailed timing comparisons.
- **Scaling Laws**: How does HOPE scale to the largest models (100B+ parameters)? Multi-timescale optimization might have different scaling properties.
- **Training Stability**: Self-modifying models (where the update rule itself is learned) could have stability issues; the reproduction's CI/release automation suggests effort here, but more analysis is needed.

## Sources

- [arXiv:2512.24695 - Nested Learning: The Illusion of Deep Learning Architectures](https://arxiv.org/abs/2512.24695)
- [Google Research - Introducing Nested Learning: A new ML paradigm for continual learning](https://research.google/blog/introducing-nested-learning-a-new-ml-paradigm-for-continual-learning/)
- [GitHub - kmccleary3301/nested_learning](https://github.com/kmccleary3301/nested_learning)
- [VentureBeat - Google's 'Nested Learning' paradigm could solve AI's memory and continual learning problem](https://venturebeat.com/ai/googles-nested-learning-paradigm-could-solve-ais-memory-and-continual/)
- [MarkTechPost - Nested Learning: A New Machine Learning Approach for Continual Learning](https://www.marktechpost.com/2025/11/08/nested-learning-a-new-machine-learning-approach-for-continual-learning-that-views-models-as-nested-optimization-problems-to-enhance-long-context-processing/)
- [Medium - Nested Learning: A New Paradigm for Continual Machine Learning](https://medium.com/@vfcarida/nested-learning-a-new-paradigm-for-continual-machine-learning-14e37828d08f)
- [PyPI - nested-learning package](https://pypi.org/project/nested-learning/)
