# We Benchmarked AI Agent Memory Over 10 Simulated Months

| | |
|---|---|
| **Source** | Reddit r/ClaudeAI |
| **URL** | [old.reddit.com/r/ClaudeAI/comments/1r59br8](https://old.reddit.com/r/ClaudeAI/comments/1r59br8/we_benchmarked_ai_agent_memory_over_10_simulated/) |
| **Researched** | 2026-02-15 |

## Overview

OMEGA, an open-source persistent memory system for AI agents, conducted the first longitudinal benchmark testing memory degradation at scale. MemoryStress simulated 1,000 agent sessions over 10 months with 583 facts and adversarial conditions. Key finding: all systems degrade significantly after ~200 sessions, but the degradation mechanism differs fundamentally between context-window architectures (cliff failures) and persistent stores (graceful noise dilution).

## Key Findings

- **Degradation threshold: ~200 sessions.** After this point, retrieval accuracy drops substantially as memory accumulates, regardless of the underlying architecture.

- **Architecture matters more than embeddings.** Traditional approaches of better embeddings or larger context windows don't solve the problem. Active memory management (consolidation, temporal decay, type-aware retention) is essential.

- **Context-window systems have cliff failures; persistent systems degrade gracefully.** Compression-based architectures (Mastra, MemGPT) must evict old data when fixed token limits are exceeded. Persistent architectures (OMEGA) have no built-in ceiling but face increasing retrieval noise in larger stores.

- **Simple approaches fail in production.** Markdown files or flat context injection work for weeks but collapse over months when memory exceeds ~200 sessions.

- **MemoryStress baseline: 32.7% accuracy (98/300 questions).** Testing at 1,000 sessions with adversarial multi-agent conditions versus LongMemEval's ~40 clean sessions. Not a flaw in the benchmark—it deliberately exposes real failure modes.

- **Per-question-type performance reveals specific weaknesses:**
  - Temporal ordering: 41.2% (strong)
  - Fact recall: 37.5% (baseline)
  - Cold start (fresh agent): 37.5% (persisted state works)
  - Cross-agent recall: 31.2% (unscoped fallback helps)
  - Single-mention facts: 27.7% (query augmentation partially mitigates)
  - Contradiction resolution: 21.4% (hardest—requires retrieval + reasoning)

- **Cost remains linear, not quadratic.** Running 1,000 sessions costs $4.06 total (~4¢ per correct answer). Retrieval-based systems scale cleanly; compression-based systems regress quadratically as context regenerates with every cycle.

## Technical Details

### MemoryStress Design

The benchmark tests in three phases:
- **Phase 1 (sessions 1–100):** Foundation—clean baseline retrieval
- **Phase 2 (sessions 101–500):** Growth—volume increases, contradictions emerge
- **Phase 3 (sessions 501–1,000):** Stress—dense, high-entropy, multi-topic sessions

Questions span 7 categories:
1. Fact recall (direct memory access)
2. Temporal ordering (date-aware retrieval)
3. Preference recall (persistent agent preferences)
4. Contradiction resolution (choosing most recent version)
5. Single-mention recall (finding buried facts)
6. Cross-agent recall (facts created by other agents)
7. Cold start recall (new agent instance accessing persisted memories)

### OMEGA's Architecture

Persistent vector store (SQLite + ONNX embeddings, CPU-only, no cloud):
- **Active memory management:** Consolidation (merge duplicates), intelligent decay (low-value memories fade), type-based retention (decisions persist forever; summaries expire in ~1 day)
- **Semantic graph layer:** Memories auto-link by similarity; relevant context surfaces through traversal rather than flat injection
- **Relevance scoring with temporal decay:** Recency boosted 1.0→1.8× multiplier; older facts deprioritized during retrieval
- **Contradiction-aware retrieval:** When multiple notes cover the same topic, explicitly prefer most recent via chronological sorting

### Optimization Techniques (MemoryStress v1→v5)

Starting baseline: 27.3% (82/300). Final: 32.7% (98/300).

1. **Contradiction-aware RAG prompt** (+5 correct): Explicit instruction to always use most recent version when multiple notes exist
2. **Query augmentation** (+4 correct): GPT-4-mini generates 3 alternative search queries per question; merge results
3. **Recency boosting** (+3 correct): 1.8× multiplier on scores for recent memories
4. **Fact extraction at ingest** (+3 correct): Extract discrete facts as separate entries; creates additional semantic hooks
5. **Cross-agent fallback** (+2 correct): Unscoped retrieval pass when agent-scoped results insufficient

## Implications

### For practitioners building long-running agents

- **Don't use flat markdown files for production agents.** They work as proof-of-concept but fail ~200 sessions in. Plan for active memory management from day one.

- **Architecture choice is irreversible later.** Fixed-window systems (Mastra's 70k token context) face hard limits. Early adoption of persistent architectures avoids costly refactoring when agents mature.

- **Retrieval is the bottleneck, not storage.** The problem isn't "where do memories go?" It's "how do we find the right memory when there are thousands?" Query augmentation, semantic graphs, and temporal weighting outperform brute-force embedding strength.

- **Contradiction handling is under-solved.** 21.4% accuracy on contradiction resolution suggests even state-of-the-art systems struggle when agents must navigate conflicting historical states. Explicit recency signaling (timestamps, version markers) in memory structure matters.

- **Multi-agent systems require careful scoping.** Cross-agent recall only hits 31.2%. Agents sharing memory need explicit separation (agent IDs, context tags) or fallback mechanisms to avoid contamination and false positives.

### For memory system designers

- **Longitudinal testing exposes what static benchmarks hide.** LongMemEval scores (95%+) are meaningless without degradation curves over 1,000 sessions. The community should adopt similar stress tests.

- **Persistent > compression for agent memory.** Context packing trades off query flexibility for a hard token ceiling. At 500+ sessions, retrieval-based lookup outperforms context regeneration both in accuracy and cost.

- **Temporal decay is non-negotiable.** Simple consolidation (dedup) helps but isn't enough. Coupling decay rates to memory type (decisions: persistent, summaries: ~1 day expiry) prevents stale context from dominating newer decisions.

- **Single-mention facts are hard.** 27.7% recall for facts mentioned once in sparse conversations suggests query augmentation alone isn't sufficient. Consider augmenting at ingest time (auto-generate keywords, extract named entities) rather than query time.

## Sources

- [OMEGA MemoryStress Benchmark Blog](https://omegamax.co/blog/why-we-built-memorystress) - Detailed technical breakdown, cost analysis, degradation curves, per-type performance
- [OMEGA GitHub Repository](https://github.com/omega-memory/core) - Open-source implementation, adaptable benchmark harness
- [OMEGA Quickstart Guide](https://omegamax.co/quickstart) - Setup and architecture documentation
- [Reddit discussion: r/ClaudeAI](https://old.reddit.com/r/ClaudeAI/comments/1r59br8/we_benchmarked_ai_agent_memory_over_10_simulated/) - Community feedback, real production use cases (200-session threshold validated by BP041 in comments)
