# Playing Civilization VI with a Computer-Use Agent

| | |
|---|---|
| **Source** | r/LocalLLaMA |
| **URL** | [old.reddit.com/r/LocalLLaMA/comments/1qtqy6f/](https://old.reddit.com/r/LocalLLaMA/comments/1qtqy6f/playing_civilization_vi_with_a_computeruse_agent/) |
| **Researched** | 2026-02-02 |

## Overview

A practitioner shared initial progress on a computer-use agent playing Civilization VI without structured game state APIs. Rather than relying on MCP-provided data, the agent reads the screen directly via vision language models, interprets the UI, and controls the game with keyboard and mouse—exactly like a human player. After one week of experiments, the agent demonstrated basic competency with the game interface and performed its first meaningful strategic actions.

## Key Points

- **Vision-Only Control**: The agent receives no structured game state via APIs. It operates purely on visual input and mouse/keyboard actions, forcing it to handle the complexity of interpreting a real, unstructured UI rather than clean, API-controlled environments.

- **Long-Horizon Strategic Challenge**: Civilization VI demands long-horizon, non-structured decision-making across science, culture, diplomacy, and warfare. The sheer breadth of strategic considerations—city building, tech trees, unit control, diplomatic relations—pushes agent capability boundaries.

- **Critical Limitation: Context and Memory**: Community feedback highlighted the core technical bottleneck: a vision-only VLM cannot maintain sufficient context to manage an entire civilization across hundreds of turns. As one commenter noted, purely reactive, pixel-to-click agents struggle with global state reasoning; they need "a higher dimensional representation of the game state beyond click city open UI element click building."

- **Scaffolding Requirement**: Even the best vision models miss critical details without heavy scaffolding. The author acknowledged that trying to handle everything end-to-end without structure doesn't work well. This mirrors broader findings in vision-language-action research where SFT (supervised fine-tuning) with human-annotated data has been the dominant approach, though RFT (reinforcement fine-tuning) is emerging.

- **Model Choice**: Currently using Gemini; initial experiments with Claude showed struggles with GUI interaction and control, leading to reliance on Gemini.

- **Not Yet Open Source**: The project is undergoing refactoring and active experimentation. Plans to open-source once stabilized.

## Technical Details

### Agent Architecture
- **Visual Perception**: VLM interprets screen state and UI elements
- **Planning**: Combines visual interpretation with game-state reasoning
- **Control**: Keyboard and mouse actions drive gameplay
- **Constraint**: No high-level game state representation or memory structure beyond what fits in context

### The Memory Problem
The central technical challenge is managing long-horizon context. Early-game states (scouting, settling cities) are tractable, but as civilizations expand across multiple eras, tracking:
- City status, production queues, happiness
- Unit locations and movement plans
- Tech tree progress and policy decisions
- Diplomatic relations and promises
- Resource flows and trade routes

...becomes intractable with only pixel-to-action capability. Turn-based gameplay amplifies the problem: each turn represents a discrete decision point where the agent must understand and act on global state.

### Related Approaches

**CivRealm** (BigAI) provides structured tensor and language APIs for Freeciv-like environments, supporting both RL and LLM agents. This allows agents to access game state directly, sidestepping the vision-perception bottleneck.

**Vox Deorum** (v0.6.1, released January 2026) implements a hybrid LLM+X architecture for Civilization V. The system separates concerns: LLMs handle macro-strategic reasoning (victory types, diplomacy personas, economic/military strategy, policies, technologies), while subsystems manage tactical execution. Research across 2,327 complete games demonstrates that LLMs achieve competitive end-game play with distinct playstyles diverging from both algorithmic AI and each other.

### VLM-Based GUI Agent Research Landscape
CVPR 2025 and ICCV 2025 show significant progress on GUI agents:
- **ShowUI**: Vision-Language-Action model for general GUI automation
- **UIPro**: Specializes in interaction capability for GUI agents
- **InternVL3**: Enhanced tool usage and GUI agent capabilities

However, production systems still face latency and reliability challenges. End-to-end latency remains "far from human speed" despite state-of-the-art accuracy on benchmarks.

## Implications

### For Practitioners Building Agents

1. **Vision-Only is Insufficient for Complex Reasoning Tasks**: Using VLMs for pixel-to-action control works for narrow, closed-loop tasks (e.g., filling a form, navigating a linear workflow). For open-ended, multi-turn strategic problems with large state spaces, hybrid architectures—combining vision perception with structured reasoning layers—are essential.

2. **Context and Memory Architecture Matters**: The tension between low-level control (mouse clicks, keyboard) and high-level strategic memory is the core technical challenge. Solutions like Vox Deorum (separate macro reasoning from tactical execution) or CivRealm (structured state APIs) show that decoupling perception from reasoning pays dividends.

3. **Scaffolding Beats End-to-End**: Pure end-to-end vision-based agents fail on complex tasks. Effective systems add structure: semantic understanding of UI elements, game-state abstraction layers, or hybrid prompting strategies. This mirrors the broader ML trend: unstructured end-to-end learning has fundamental limits for reasoning-heavy problems.

4. **Model Selection Impacts GUI Performance**: Gemini outperformed Claude on this task. Different models have varying GUI understanding capabilities—worth testing empirically on your target domain.

5. **Long-Horizon Planning Requires Explicit State Management**: If agents must reason over hundreds of turns or decisions, explicit logging, summary generation, or hierarchical decomposition becomes necessary. VLMs alone won't carry full game state across long sequences.

### For Research Directions

The Civilization VI challenge is an excellent stress test for VLA capabilities. Success would require breakthroughs in:
- **Memory and Context Management**: How to compress and maintain strategic context over hundreds of turns
- **Hierarchical Reasoning**: Decomposing grand strategy into tactical sub-problems
- **UI Grounding**: Accurate semantic understanding of complex, interdependent UI elements
- **Multi-Turn Planning**: Maintaining coherent goals and strategies across extended decision horizons

## Sources

- [old.reddit.com/r/LocalLLaMA/comments/1qtqy6f/playing_civilization_vi_with_a_computeruse_agent/](https://old.reddit.com/r/LocalLLaMA/comments/1qtqy6f/playing_civilization_vi_with_a_computeruse_agent/) - Original Reddit post with discussion
- [github.com/bigai-ai/civrealm](https://github.com/bigai-ai/civrealm) - CivRealm: structured API-based Freeciv environment
- [github.com/CIVITAS-John/vox-deorum](https://github.com/CIVITAS-John/vox-deorum) - Vox Deorum: hybrid LLM+X architecture for Civ V
- [arxiv.org/abs/2512.18564](https://arxiv.org/abs/2512.18564) - Vox Deorum paper (December 2025): lessons from 2,327 test games
- [forums.civfanatics.com/threads/beta-vox-deorum-0-6-1-play-civilization-with-llms.701133/](https://forums.civfanatics.com/threads/beta-vox-deorum-0-6-1-play-civilization-with-llms.701133/) - Vox Deorum 0.6.1 beta thread
- [CVPR 2025 Visual Agents papers](https://voxel51.com/blog/visual-agents-at-cvpr-2025/) - ShowUI and GUI agent research landscape
