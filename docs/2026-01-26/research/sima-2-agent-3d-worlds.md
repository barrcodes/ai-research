---
title: SIMA 2: An Agent that Plays, Reasons, and Learns With You in Virtual 3D Worlds
source: DeepMind Blog
url: https://deepmind.google/blog/sima-2-an-agent-that-plays-reasons-and-learns-with-you-in-virtual-3d-worlds/
researched_at: 2026-01-26T00:00:00Z
---

# SIMA 2: An Agent that Plays, Reasons, and Learns With You in Virtual 3D Worlds

## Overview

SIMA 2 represents a significant advancement in embodied AI, moving beyond reactive instruction-following to demonstrate genuine agentic reasoning in complex 3D environments. By integrating Gemini as its reasoning backbone with screen-based observation and virtual input control, the agent achieves unprecedented adaptability across diverse games while explicitly articulating its decision-making process.

## Key Points

- **Integrated reasoning engine**: Gemini models power multi-step planning and self-explanation, enabling the agent to decompose complex goals and narrate its own reasoning
- **Hybrid training signal**: Combines human demonstration videos with Gemini-generated synthetic labels, bootstrapping explanation capability without requiring human annotation for every decision
- **Game-agnostic interaction**: Operates via screen observation + keyboard/mouse control without access to game internals, enabling deployment across any game engine
- **Concept abstraction**: Transfers learned skills across semantically different domains (mining → harvesting, navigation patterns across genres)
- **Self-improvement loop**: Demonstrates autonomous learning via trial-and-error with Gemini-based feedback in novel environments, reducing data collection costs
- **Generalization to unseen worlds**: Shows "unprecedented adaptability" when tested in Genie 3-generated procedural environments

## Technical Details

**Architecture**: SIMA 2 combines vision-language models (perceiving game state) with planning capabilities. The system maintains stateful reasoning across multi-turn interactions, enabling iterative refinement of strategies.

**Training Data**: Mix of:
- Human demonstration videos with language labels
- Gemini-generated action descriptions and goal decompositions
- Synthetic data from procedural environments

**Tested Environments**: *Valheim*, *No Man's Sky*, *Minecraft* (MineDojo), *ASKA* — spanning sandbox exploration, survival mechanics, and structured objectives.

**Performance Gap**: Closes "a significant portion of the gap to human performance" relative to SIMA 1, with substantially higher success rates on multi-step tasks.

**Limitations**: Acknowledged struggles with "very long-horizon, complex tasks requiring extensive multi-step reasoning" — indicating current ceiling for plan depth and recovery from execution errors.

## Implications

For practitioners, SIMA 2 validates the architectural pattern of separating perception (vision) from reasoning (language models) from control (action execution). The explicit reasoning layer is architecturally significant — it enables:

1. **Debuggability**: Agent articulates its reasoning, making failure modes interpretable vs. black-box prediction
2. **Reusability**: Learned abstractions (tool use, navigation) transfer across domains without retraining
3. **Scalability**: Self-improvement loop reduces annotation burden for new environments

The primary limitation is computational cost and multi-step reasoning depth. For robotics applications, you should expect this pattern (vision + LLM reasoning + low-level control) to become standard, but be prepared for current agents to decompose long-horizon tasks into achievable subtasks rather than executing monolithic plans.

The screen-observation design choice trades raw efficiency for universal deployment — significant for sim-to-real transfer where learning in diverse simulation engines matters more than raw speed.

## Related

- [SIMA 1 original work](https://deepmind.google/blog/sima-scalable-instructable-multiworld-agent/) — Previous generation with instruction-following baseline
- [Genie: Generative Interactive Environments](https://deepmind.google/blog/genie-generative-interactive-environments/) — Procedural world generation used for generalization testing
- [Scaling language-model-based multi-task agents](https://research.google/blog/) — Architectural patterns for multi-modal agent scaling
