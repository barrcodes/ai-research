# SAE Steering & Claude

| | |
|---|---|
| **Source** | Anthropic Research + Hugging Face Blog |
| **URL** | [anthropic.com/research/evaluating-feature-steering](https://www.anthropic.com/research/evaluating-feature-steering) |
| **Researched** | 2026-02-14 |

## Overview

Anthropic's sparse autoencoder (SAE) research extracts 34 million interpretable features from Claude 3 Sonnet's activations, enabling direct manipulation of model behavior through activation steering—modifying internal representations without retraining. Feature steering successfully controls semantic dimensions like bias, tone, and safety, but critically fails for syntactic tasks like JSON validation, revealing a fundamental architectural limitation: steering operates on continuous semantic axes, not discrete syntactic constraints.

## Key Points

- **Extraction capability**: Dictionary learning on Claude activations identifies individual features tied to specific concepts (Golden Gate Bridge obsession, gender bias, deceptive language, code quality)
- **Sweet spot discovery**: Steering factors between -5 and +5 preserve model capabilities; extreme values degrade performance significantly
- **Targeted success**: Amplifying "pro-life" feature increased anti-abortion responses by 50%; "Neutrality" features reduced bias across nine dimensions
- **Off-target risks**: A "gender bias awareness" feature unexpectedly increased age bias by 13%—steering effects propagate unpredictably across domains
- **Semantic/syntactic boundary**: Steering succeeds for behavioral tendencies (continuous scales) but fails completely for structural correctness (binary validity, state tracking)

## Technical Details

**SAE Training & Feature Discovery:**
- 34 million features extracted from Claude 3 Sonnet using dictionary learning
- Each feature corresponds to interpretable semantic concepts
- Features captured safety-critical behaviors (bias, deception) alongside personality traits

**Evaluation Framework:**
- Capabilities: MMLU, PubMedQA benchmarks
- Bias evaluation: BBQ dataset (nine categories)
- Range tested: -20 to +20 steering intensity
- 10 samples per question for statistical power

**Why Steering Fails for JSON:**

| Property | Steering | JSON Requirement |
|----------|----------|------------------|
| **Nature** | Semantic modulation | Syntactic constraint |
| **Mechanism** | Continuous axis adjustment | Discrete state machine |
| **Correctness** | Soft (more/less X) | Binary (valid/invalid) |
| **Result** | 24.4% valid JSON vs 86.8% baseline | Degradation across all variants |

The core problem: steering operates on activation dimensions already learned by the base model. JSON syntax requires stateful tracking (nesting depth, quote pairs, bracket matching) that isn't naturally encoded in semantic features.

## Implications

**For Agentic Systems:**

1. **Control strategy selection is critical**: Use activation steering for behavior modification (safety guardrails, bias reduction, personality tuning), but use constrained decoding (FSM-based logits masking) for structured outputs. Conflating these approaches degrades both.

2. **Unintended consequences are real**: Cross-domain steering effects observed in bias experiments indicate that feature activation is loosely coupled across semantic domains. Single-feature steering in production requires comprehensive downstream monitoring.

3. **Composition risk**: Combining steering with fine-tuning can push models out of task-specific activation subspaces, causing unexpected degradation. Baseline models respond differently than specialized variants.

4. **Deployment barrier**: Anthropic's own researchers emphasized that "careful evaluations would be necessary before deploying feature steered models in practice." The sweet spot preservation (factors -5 to +5) is a narrow operational window.

**Architectural Takeaway:**

For engineers building agentic systems on Claude internals, understand that you're working with two distinct control layers: semantic steering for behavioral modulation, and token-level constraints for structural guarantees. Attempting to control syntax through semantic steering is like trying to maintain invariants with soft assertions—it simply won't work. This boundary mirrors the classical separation between high-level policy (what to say) and low-level execution (how to format it).

## Sources

- [Anthropic Research: Evaluating Feature Steering](https://www.anthropic.com/research/evaluating-feature-steering) - Official evaluation framework and findings on safe steering bounds
- [Transformer Circuits: Scaling Monosemanticity](https://transformer-circuits.pub/2024/scaling-monosemanticity/) - Foundational SAE extraction and feature discovery on Claude 3 Sonnet
- [Hugging Face Blog: SAE Steering Limitations](https://huggingface.co/blog/MaziyarPanahi/sae-steering-json) - Analysis of semantic vs. syntactic steering failure modes and constrained decoding alternatives
