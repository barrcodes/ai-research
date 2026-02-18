# Claude Sonnet 4.6 vs 4.5: Spatial Reasoning Performance

| | |
|---|---|
| **Source** | Anthropic, LLM-Stats, Academic Research |
| **URL** | [anthropic.com/news/claude-sonnet-4-6](https://www.anthropic.com/news/claude-sonnet-4-6) |
| **Researched** | 2026-02-18 |

## Overview

Claude Sonnet 4.6 (released February 17, 2026) demonstrates substantial improvements in spatial reasoning and abstract pattern recognition compared to 4.5. The most striking data point: ARC-AGI-2 performance improved 4.3x (13.6% → 58.3%), indicating a fundamental shift in how the model reasons about novel visual patterns it hasn't encountered during training.

## Key Points

- **ARC-AGI-2 (Spatial Reasoning Benchmark)**: 4.3x improvement from Sonnet 4.5 (13.6%) to Sonnet 4.6 (58.3%) with 120k thinking tokens. This benchmark specifically tests abstract reasoning on grid-based visual puzzles designed to resist memorization.

- **ARC-AGI-1 Performance**: Sonnet 4.6 achieves 86.50% on ARC-AGI-1, ranking #2 globally. This represents mastery of established spatial reasoning patterns.

- **Visual-Spatial Consistency**: Early users report that Sonnet 4.6 produces notably more polished visual outputs with better layouts and design sensibility than previous versions, suggesting improved spatial understanding of design principles.

- **Long-Context Spatial Reasoning**: The 1M token context window (beta) enables reasoning across extended spatial configurations and multi-step spatial transformations without losing coherence.

- **Computer Use Improvements**: OSWorld-Verified scores show 72.5% (4.6) vs 61.4% (4.5)—an 18% relative improvement. This involves spatial navigation and visual interface understanding.

## Technical Details

### ARC-AGI Benchmark Architecture

ARC-AGI-2 tests models with input-output grid pairs (1x1 to 30x30) using colored cells (0-9). Models must identify transformation rules from examples and apply them to test cases. The benchmark emphasizes:

- **Compositional Generalization**: Understanding how spatial rules compose
- **Novel Pattern Recognition**: Zero-shot transfer to unseen transformations
- **Multi-step Spatial Logic**: Chains of spatial operations

The 4.3x improvement suggests Sonnet 4.6's training or architecture changes enable better systematic reasoning about spatial relationships rather than pattern memorization.

### Performance Across Related Domains

- **GPQA (Expert-Level Reasoning)**: 90% accuracy—includes questions requiring spatial visualization
- **Humanity's Last Exam**: 38% overall, positioning Sonnet 4.6 #5 globally; spatial problem-solving critical for mathematics/physics domains
- **GDPval-AA (Office Tasks)**: 1633/3000 Elo score—spreadsheet navigation, form filling, and visual document understanding require spatial reasoning

## Implications

**For Practitioners:**

1. **Agentic Systems**: Sonnet 4.6 is now viable for tasks requiring visual understanding and spatial planning (UI automation, document parsing, form-filling) without escalating to Opus. The 18% improvement in computer use suggests production-grade reliability.

2. **Cost-Performance Inflection**: At $3/$15 per million tokens (unchanged pricing), you gain ~4x improvement in spatial reasoning benchmarks. This reshapes cost-benefit calculations for vision-dependent workflows.

3. **Architectural Trade-offs**: The improvement likely comes from training enhancements or extended thinking rather than parameter count increases. This suggests spatial reasoning was a bottleneck in 4.5 that's been directly addressed.

4. **Thinking Token Requirements**: Sonnet 4.6 achieves 58.3% on ARC-AGI-2 with 120k thinking tokens allocated. Budget thinking tokens for spatial reasoning tasks; raw reasoning capacity is still constrained compared to 4.5 without token extensions.

5. **Design & Synthesis**: User reports of "better layouts, animations, and design sensibility" suggest Sonnet 4.6 understands spatial composition principles. Relevant for frontend code generation, UI design prompting, and design review automation.

## Critical Limitations

- **Absolute Performance**: 58.3% on ARC-AGI-2 is respectable but still requires external tools or verification for production use in high-stakes spatial reasoning (robotics, CAD, precision navigation).
- **Domain-Specific Gaps**: Improvements appear general. No evidence of specialized advances in 3D spatial reasoning, geometric theorem proving, or technical spatial documentation.
- **Benchmark vs. Reality**: ARC-AGI tests synthetic grid puzzles. Real-world spatial tasks (parsing complex diagrams, multi-page spatial workflows) may show different relative improvements.

## Architecture Questions

1. **What changed?** The public announcement doesn't detail whether improvements come from architecture changes, training data augmentation, or inference-time reasoning allocation.

2. **Scaling trajectory**: Does spatial reasoning improve linearly with thinking tokens? At what point does allocation become cost-prohibitive?

3. **Generalization**: Does the improvement on grid-based ARC-AGI transfer to continuous spatial domains (3D geometry, navigation, real image understanding)?

## Sources

- [Introducing Claude Sonnet 4.6](https://www.anthropic.com/news/claude-sonnet-4-6) - Official Anthropic announcement with benchmark details
- [Claude Sonnet 4.6: Pricing, Context Window, Benchmarks, and More](https://llm-stats.com/models/claude-sonnet-4-6) - Comprehensive benchmark compilation and rankings
- [Stuck in the Matrix: Probing Spatial Reasoning in Large Language Models](https://arxiv.org/html/2510.20198v1) - Academic research on LLM spatial reasoning capabilities
- [Claude Sonnet 4.6 now available in Amazon Bedrock](https://aws.amazon.com/about-aws/whats-new/2026/02/claude-sonnet-4.6-available-in-amazon-bedrock/) - Enterprise availability
