# AIRS-Bench - First Agent Lifecycle Benchmark

| | |
|---|---|
| **Source** | Facebook Research / arXiv |
| **URL** | [arxiv.org/abs/2602.06855](https://arxiv.org/abs/2602.06855) |
| **Researched** | 2026-02-09 |

## Overview

AIRS-Bench is the first benchmark measuring agent performance across the complete research lifecycle—from problem formulation through iterative experimentation to final submission. Unlike LLM benchmarks that test isolated capabilities, AIRS-Bench evaluates whether agents can autonomously solve open machine learning problems without starter code, spanning 20 tasks across NLP, mathematical reasoning, biochemistry, and time series forecasting.

## Key Points

- **Full Lifecycle Evaluation**: Assesses agents on idea generation, experiment analysis, and iterative refinement—not single-turn problem solving. Tasks provide only problem description and data; agents must implement solutions end-to-end.

- **Unsaturated Benchmark**: Results show agents exceeded human SOTA in 4/20 tasks but fell far short in 16 others. Even when agents won, they "do not reach the theoretical performance ceiling," indicating substantial headroom for improvement in autonomous research.

- **Standardized Task Framework**: Each task includes metadata, problem descriptions, dataset preparation, and evaluation scripts. The format supports multiple agent scaffolds (One-Shot, Greedy, ReAct) and frameworks (aira-dojo parallel harness, MLGym linear harness), enabling rigorous cross-agent comparison.

- **Non-Linear Scoring**: Uses normalized scoring φₜ(s) = -log₁₀(|s - s_opt|) to account for task heterogeneity, with metrics including valid submission rate and Elo ratings. This prevents high-variance tasks from dominating aggregate scores.

## Technical Details

**Task Composition**: 20 problems from peer-reviewed ML papers across NLP (code generation, language modeling), mathematics, biochemistry, and time-series forecasting. Tasks use 16 datasets with diverse evaluation targets (accuracy, error rates, ranking metrics).

**Agent Architecture**: Three-component system—agents (LLM + scaffolding), scaffolds (exploration mechanisms), harnesses (process management). The benchmark abstracts over agent implementation details, making it framework-agnostic while enforcing consistent evaluation protocols.

**Baseline Results**: Frontier models (GPT-oss-120b) achieved ~0.522 ± 0.018 normalized score, indicating 48% average performance gap to theoretical ceiling. The spread across tasks reveals capability gaps in specific domains (mathematical proofs, complex simulations).

## Implications

**For Agent Developers**: AIRS-Bench exposes critical gaps in long-horizon research capabilities versus single-task LLM benchmarks. If your agents must *discover* solutions iteratively rather than choose from options, this benchmark matters. Focus areas: persistent state management across experiments, failure recovery without human guidance, principled experiment design.

**For Architecture Decisions**: The standardized task format enables reproducible agent comparison but requires implementing evaluation harnesses for each framework. Teams using custom agent scaffolding should expect non-trivial integration effort. The benchmark's unsaturated nature (agents far from ceiling) suggests room for architectural innovation before hitting hard limits.

**For Evaluation Strategy**: Traditional LLM benchmarks (MMLU, HumanEval) test knowledge/capability in isolation. AIRS-Bench tests *composition*—can the agent chain observation → hypothesis → implementation → evaluation → iteration without external feedback? Expect to see this shift as agentic systems mature from assistants to autonomous researchers.

## Sources

- [arXiv:2602.06855 - AIRS-Bench: a Suite of Tasks for Frontier AI Research Science Agents](https://arxiv.org/abs/2602.06855)
- [GitHub - facebookresearch/airs-bench](https://github.com/facebookresearch/airs-bench)
- [Bhavul Gauri - AIRS-Bench announcement on X](https://x.com/BhavulGauri/status/2020938358982394332)
