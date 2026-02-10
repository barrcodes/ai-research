# New SOTA on ARC-AGI Benchmark: Learned Test-Time Reasoning Breaks 50% Barrier

| | |
|---|---|
| **Source** | Poetiq AI, ARC Prize Leaderboard |
| **URL** | [poetiq.ai/posts/arcagi_verified/](https://poetiq.ai/posts/arcagi_verified/) |
| **Researched** | 2026-02-04 |

## Overview

Poetiq AI has established a new state-of-the-art on the ARC-AGI-2 benchmark, achieving 54% accuracy at $30.57 per task—the first method to break the 50% threshold and solve a majority of problems. This represents a significant advance over the previous best score of 45% (Gemini 3 Deep Think at $77.16/task), accomplished at less than half the cost. Their approach uses a meta-system built on top of existing frontier models (Gemini 3, GPT-5.1) without requiring model fine-tuning, demonstrating the power of sophisticated test-time reasoning and iterative problem-solving strategies.

## Key Points

- **54% accuracy on ARC-AGI-2 Semi-Private set** — Officially verified by ARC Prize, surpassing previous best of 45% while reducing cost by 60%
- **Learned test-time reasoning paradigm** — Poetiq's meta-system uses iterative refinement: generating solutions, receiving feedback, analyzing, and refining without pre-commitment to single reasoning paths
- **Model-agnostic architecture** — Achieves improvements across 12 model families (GPT, Claude, Gemini, Grok, open-weights) by leveraging their inherent capabilities rather than requiring specialized training
- **Redraw Pareto frontier** — Establishes entirely new cost-vs-performance curves on both ARC-AGI-1 and ARC-AGI-2, with multiple configurations ranging from pennies per task to premium solutions
- **First to reach 60% benchmark on ARC-AGI-2** — Exceeds average human test-taker performance (60% on public set), though gap remains on more difficult tasks
- **<2 attempts per problem** — Achieves this despite ARC-AGI allowing two attempts per task, indicating fundamental efficiency gains
- **Open-sourced solutions** — Publicly released code enables community reproducibility and further innovation

## Technical Details

### Methodology

Poetiq's core innovation is a meta-system that treats the prompt as an interface rather than fixed intelligence. The system:

1. **Generates candidate solutions** through LLM queries (often producing code for structured reasoning)
2. **Autonomously audits progress** — decides when solution is satisfactory or requires further refinement
3. **Iteratively refines** through chain-of-reasoning without committing to single paths
4. **Adaptively selects models and approaches** — the system decides which models to assign which subtasks and when to invoke coding vs. pure reasoning
5. **Self-terminates** when confidence threshold is reached, minimizing wasted computation

### Performance Across Models

Demonstrates substantial transference: the same meta-system adapted on open-source models (GPT-OSS-120B) generalizes to all recent frontier models tested:
- **Gemini 3**: 65-95% accuracy range (vs. 50-86% baseline)
- **GPT-5/5.1**: 40-80% accuracy range (vs. 40-73% baseline)
- **Claude Haiku 4.5**: 40-70% accuracy range (vs. 40-65% baseline)
- **Grok 4 Fast**: Achieves 60%+ despite being two orders of magnitude cheaper than premium solutions

### Key Architectural Insight

The system was adapted *before* exposure to Gemini 3 and GPT-5.1 (released Nov 13-18, 2025), and never trained on ARC-AGI-2 problems. Yet it achieved SOTA within hours of model release. This indicates:
- The reasoning patterns learned are fundamental and model-agnostic
- Effective prompting and iteration strategies transfer across model versions
- The meta-system discovers elegant reasoning methods applicable across the frontier

## Implications

### For Reasoning Benchmarks

1. **Test-time scaling emerging as critical frontier** — This represents the shift from model scale to reasoning strategy as the primary lever for capability gains. Epoch-defining for how we think about AI progress.

2. **Efficiency-capability decoupling** — Previous wisdom assumed better performance required exponentially higher compute cost. This work achieves state-of-the-art at 60% cost reduction, suggesting the frontier has significant untapped leverage in reasoning optimization.

3. **Model-agnostic reasoning strategies** — The fact that the same meta-system improves *every* model family tested suggests we're discovering truly general reasoning principles, not model-specific tricks. This has profound implications for how reasoning capabilities will evolve post-frontier-model releases.

4. **Human-level and beyond on reasoning tasks** — Exceeding average human performance (60%) on ARC-AGI-2 marks the first public benchmark of this kind. Remaining gap to expert human performance suggests distinct challenge classes, not an asymptote.

### For Architecture and Systems Design

1. **Meta-learning of reasoning strategies** — Organizations developing reasoning systems should prioritize building meta-systems that discover optimal strategies for their domain, rather than engineering fixed prompts or fine-tuning models.

2. **Cost-aware reasoning at scale** — The Pareto frontier of cost vs. performance creates decision points for practitioners. Different deployment scenarios (batch processing vs. interactive inference) now have quantified trade-offs.

3. **Iterative refinement over single-shot capability** — The advantage of <2 attempts per problem suggests that iterative, hierarchical problem decomposition (rather than monolithic reasoning) will become standard practice.

4. **Minimal model updates required** — Poetiq's open-weights-only adaptation generalizing to all frontier models implies that organizations need not wait for perfect proprietary models; effective reasoning strategies work across a rapidly-evolving model landscape.

### For AGI Progress

1. **Demonstrates clear path to higher reasoning capability** — The separation of reasoning strategy from model quality creates a multiplication effect: each new frontier model compounds with better reasoning strategies.

2. **Cost efficiency as competitive moat** — Being able to solve state-of-the-art reasoning problems at 60% cost reduction has concrete business implications and suggests efficiency will become primary differentiator in a multi-vendor frontier model landscape.

3. **Scaling hypothesis refinement** — This work suggests reasoning capability scales not only with model scale but through sophistication of test-time computation. Implies that compute-optimal frontiers are much higher than previously estimated for reasoning tasks.

4. **Meta-system accessibility** — Open-sourcing the approach (and Poetiq's public API access) means this capability will rapidly diffuse. Within weeks, most organizations can access SOTA reasoning performance, accelerating the pace of capability deployment across the ecosystem.

## Sources

- [Poetiq | ARC-AGI-2 SOTA at Half the Cost](https://poetiq.ai/posts/arcagi_verified/) - Official verification of state-of-the-art results, December 5, 2025
- [Poetiq | Traversing the Frontier of Superintelligence](https://poetiq.ai/posts/arcagi_announcement/) - Technical methodology and Pareto frontier analysis, November 20, 2025
- [ARC Prize - Leaderboard](https://arcprize.org/leaderboard) - Official ARC-AGI leaderboard with verified results
- [ARC Prize 2025 Results and Analysis](https://arcprize.org/blog/arc-prize-2025-results-analysis) - Benchmark overview and context