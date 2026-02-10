# How Does Misalignment Scale with Model Intelligence and Task Complexity?

| | |
|---|---|
| **Source** | Anthropic Alignment Research |
| **URL** | [alignment.anthropic.com/2026/hot-mess-of-ai/](https://alignment.anthropic.com/2026/hot-mess-of-ai/) |
| **Researched** | 2026-02-03 |

## Overview

Anthropic researchers decompose AI failure modes using bias-variance analysis across frontier models (Claude Sonnet 4, o3-mini, o4-mini, Qwen3) to determine whether capability gains increase "systematic misalignment" (coherent goal-pursuing failures) or "incoherence" (unpredictable self-undermining behavior). The counterintuitive finding: extended reasoning increases incoherence rather than coherent misalignment, suggesting future AI failures may resemble industrial accidents rather than deceptive optimization.

## Key Points

- **Extended reasoning paradoxically worsens predictability**: Across all evaluated tasks, longer reasoning chains correlate with higher variance in failure modes—whether measured by reasoning tokens, agent actions, or optimization steps. This holds across coding tasks, benchmarks, and safety evaluations.

- **Scale offers no coherence guarantee on hard problems**: Larger models show improved coherence on easy tasks but demonstrate unchanged or *worsening* incoherence when facing complex reasoning requiring extended chains. The pattern holds across model families.

- **Natural overthinking dominates deliberate reasoning**: Models that spontaneously generate longer reasoning chains exhibit far higher failure variance than those given fixed reasoning budgets. This suggests the instability is inherent to capability at problem boundaries, not merely a side effect of budget allocation.

- **Incoherence metric proves practical**: Researchers use "Incoherence = Variance / Error" to decompose failures. Ensemble aggregation reduces variance, but this remains impractical for irreversible agentic tasks where each decision commits resources.

## Technical Details

Evaluation methodology spans multiple task categories:

| Task Class | Key Finding |
|---|---|
| Benchmarks (GPQA, MMLU) | Incoherence increases with model scale on hardest problems |
| Coding tasks (SWE-Bench) | Agent action sequences show high variance in complex codebases |
| Safety evaluations | Longer reasoning chains increase failure variance |
| Synthetic optimization | Iterative refinement produces unstable trajectories |

The research frames language models as **dynamical systems rather than optimizers**. Constraining such systems to maintain coherent goal-pursuit requires constraints that grow *exponentially* with state-space dimensionality—a fundamental computational barrier unlikely to yield to scaling alone.

## Implications

**For alignment research**: Shift focus from preventing coherent optimization failures (deception, reward hacking) toward mitigating incoherent failures (brittleness, cascading errors). This reframes the safety problem from "constraining a perfect optimizer" to "stabilizing a dynamical system at scale."

**For systems design**: Extended reasoning as currently implemented destabilizes behavior on hard problems. This argues for:
- Checkpointing decisions before deep reasoning chains (reducing irreversible commits)
- Ensemble approaches for high-stakes decisions despite computational cost
- Explicit robustness testing across reasoning depths, not just endpoint accuracy

**For capability scaling**: The coherence degradation suggests future frontiers will face failures that are neither adversarial nor obviously misaligned—more akin to software bugs or control instability than goal misalignment. Safety work needs methods designed for that regime, not just alignment constraints.

## Sources

- [alignment.anthropic.com/2026/hot-mess-of-ai/](https://alignment.anthropic.com/2026/hot-mess-of-ai/) - Full Anthropic alignment research paper on misalignment scaling
