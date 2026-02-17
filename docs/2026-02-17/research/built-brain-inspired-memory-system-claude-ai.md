# Brain-Inspired Memory System for Claude.ai

| | |
|---|---|
| **Source** | Hacker News (Show HN) / GitHub (claude-engram) |
| **URL** | [news.ycombinator.com/item?id=47037566](https://news.ycombinator.com/item?id=47037566) |
| **Researched** | 2026-02-17 |

## Overview

Claude-engram is a React artifact that implements a brain-inspired persistent memory system running entirely within Claude.ai's chat interface. The system models human memory formation through salience scoring, exponential decay curves, and sleep consolidation—eliminating the traditional append-only memory bloat problem plaguing current LLM implementations. Importantly, it requires no external API keys, servers, or browser extensions; storage leverages Claude's native 5MB window.storage capability.

## Key Points

- **Salience-Based Scoring**: Memories scored on four dimensions at ingestion—novelty, relevance, emotional weight, and predictive value. Scoring adapts contextually via Claude Sonnet instances.

- **Forgetting as Feature**: Exponential decay prevents unbounded memory growth. Unaccessed memories with average salience of 0.3 fade to near-zero within ~3 weeks (0.015/day decay rate). Accessed memories reset decay (+0.12 boost per access, capped at 0.5).

- **Sleep Consolidation**: Background cycle merges redundant memories into stronger entries, extracts generalized patterns from recurring themes, and prunes below-threshold content. This preserves signal while preventing scale-driven bloat.

- **Dynamic Strength Calculation**: `strength = avg_salience + retrieval_boost + consolidation_bonus - (decay_rate × age_in_days)`. Consolidated memories receive +0.2 flat bonus, reinforcing naturally-relevant memories.

- **Zero External Dependencies**: Single artifact runs in Claude.ai's built-in React environment. Installation: add artifact to chat + paste instructions into personal preferences.

## Technical Details

### Architecture

The system operates as a self-contained React component leveraging:
- Claude API calls for salience scoring and memory evaluation
- Browser localStorage (5MB quota via window.storage)
- No database, server, or authentication infrastructure

### Memory Lifecycle

1. **Ingestion**: Memory tagged with novelty, relevance, emotional weight, predictive value
2. **Retention**: Strength decays daily unless accessed or consolidated
3. **Consolidation**: Periodic merging of semantically-similar memories; pattern extraction from themes
4. **Retrieval**: Retrieved memories receive boost; boost stacks with consolidation bonus

### Design Trade-offs

**Strength:**
- Mirrors human memory's natural forgetting curve (exponential decay vs. compression)
- Avoids vector database overhead; pure attribute-based scoring
- Salience dimensions adapt to user context automatically

**Limitations (acknowledged roadmap):**
- Decay rate (0.015/day) and consolidation frequency currently constant; ideally would adapt per user interaction patterns
- 5MB storage ceiling may constrain very long-term, high-volume memory scenarios
- Still in early-stage tuning; parameters not yet optimized

## Implications

### For Practitioners

1. **Departure from Vector Stores**: This shifts memory architecture from traditional append-only logs and vector similarity to human-inspired decay mechanics. The insight that "useful memory actually works" through salience + forgetting is architecturally significant—vendors and tool builders should reconsider whether vector-only approaches are sufficient.

2. **Memory Bloat is Solvable**: Addressed not through aggressive compression or retrieval filtering, but through explicit forgetting. This reframes long-context problems; context window constraints become less critical if non-essential memories decay naturally.

3. **Claude.ai as a Development Platform**: Artifacts + localStorage + API access create a viable platform for stateful, persistent multi-turn agents without backend infrastructure. Reduces operational overhead for builders.

4. **Cognitive Science Grounding**: Implementing salience scoring and consolidation cycles based on neuroscience (hippocampal formation, slow-wave sleep) appears more effective than engineering-first approaches. This validates interdisciplinary design in AI tooling.

5. **Community Gap**: Current "memory" features in LLMs (including Anthropic's new official persistent memory for Claude) treat memory as simple append-or-retrieve. This demonstrates a clear gap—simpler doesn't mean better, and sophisticated memory modeling can run client-side.

### For Architecture Design

- **Stateful LLM Agents**: Systems requiring long-term context retention should explore decay-based memory instead of retrieval-only approaches
- **Storage-Constrained Environments**: 5MB footprints with intelligent consolidation may address edge use cases (mobile, embedded agentic layers)
- **User Adaptation**: Memory systems that adapt their retention policies per-user interaction patterns (listed as future work) could substantially improve personalization

## Sources

- [Show HN: Claude-engram – Brain-inspired persistent memory, runs inside Claude.ai](https://news.ycombinator.com/item?id=47037566) - Technical discussion with creator (mlapeter)
- [GitHub - mlapeter/claude-engram](https://github.com/mlapeter/claude-engram) - Project repository
- [Claude's "Persistent Memory" Is Coming? We've Already Built It - Medium](https://medium.com/@memU_ai/claudes-persistent-memory-is-coming-we-ve-already-built-it-48395e8c5ece) - Comparative analysis of memory implementations
- [Architecting a memory engine inspired by the human brain - supermemory.ai](https://supermemory.ai/blog/memory-engine/) - Related neuroscience-informed memory architecture patterns
