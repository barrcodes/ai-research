# Opus 4.6 vs Sonnet 4.6: Agentic Benchmarking Results

| | |
|---|---|
| **Source** | r/ClaudeAI |
| **URL** | [old.reddit.com/r/ClaudeAI/comments/1r9jf2j/](https://old.reddit.com/r/ClaudeAI/comments/1r9jf2j/i_benchmarked_opus_46_vs_sonnet_46_on_agentic_pr/) |
| **Researched** | 2026-02-20 |

## Overview

A detailed benchmark comparing Opus 4.6 and Sonnet 4.6 on real-world agentic tasks (PR review and browser QA) revealed that the old cost-based routing heuristic ("always use Opus for important work") is obsolete. Sonnet 4.6 demonstrates superior performance on breadth-first adversarial analysis while maintaining 1.6x-5.5x cost advantage, while Opus justifies its premium only for multi-layer depth-first reasoning tasks.

## Key Findings

### PR Review Benchmark (4K-line backend refactoring PR)

**Performance:**
- Sonnet found 9 issues on average; Opus found 6
- Zero false positives from either model
- Combined coverage: 11 distinct findings when both are used
- Clear complementary strength pattern: each model had different blind spots

**Cost & Speed:**
- Opus: ~$0.86/session, 138s runtime (1.76x Sonnet cost, 26% slower)
- Sonnet: ~$0.49/session, 102s runtime

**Quality Pattern - Issue Types:**
- **Sonnet's strength**: Breadth-first adversarial catches (auth inconsistency between mutations, unsafe casts on AI-generated data, mock mismatches, production error pattern matching)
- **Opus's strength**: Deep multi-layer tracing (caught a 3-layer error handling bug spanning fetch utility → service layer → router, required 14 extra tool calls)
- **Consistency**: Opus showed higher run-to-run consistency; Sonnet had more variance but higher ceiling

### Browser QA Benchmark (7-step form flow: sign-in → edit → save → verify → logout)

**Pass Rates:**
- Both models: 7/7 pass rate (100% success)

**Cost & Runtime:**
- Sonnet: 3.6 minutes, ~$0.24/run
- Opus: 8.0 minutes, ~$1.32/run (5.5x more expensive)

**Behavior Differences:**
- **Sonnet**: Clean execution, no recovery needed, followed spec exactly
- **Opus**: Exceeded spec with senior QA instincts—reloaded page to verify DB persistence (not just DOM state), self-initiated test data cleanup without being asked

**Cost Implications:**
- CI scale: 10 browser tests per PR = **$2.40 (Sonnet) vs $13.20 (Opus)**

## Technical Details

### Benchmark Methodology

- Isolated context windows (no cross-model contamination)
- 10 independent sessions per model per task
- Both models ran at "High Effort" in Claude Code
- No statistical difference found from orchestrator choice (tested both Sonnet and Opus as orchestrators)
- Browser QA received identical instruction markdown from upstream agent
- PR selected deliberately large to stress-test models

### Agent Configuration Architecture

The benchmark revealed optimal model placement in a production pipeline:

1. **Architect (Opus)**: Deep-thinker, multi-layer bug tracing, spec alignment, test coverage validation
2. **Skeptic (Sonnet)**: Adversarial analysis, edge cases, security vulnerabilities, Sentry cross-reference
3. **Simplifier (Sonnet)**: Complexity detection, dead code, convention violations (deterministic task)
4. **Rule-Reviewer (Sonnet)**: Hard anti-pattern enforcement (mechanical, fully deterministic)
5. **Triage (Opus)**: EM function, risk classification, routing to specialized agents
6. **QA Pre-flight (Sonnet)**: Route/selector discovery, fixture data gathering (moved from Opus)
7. **Browser-Tester (Sonnet)**: Chrome automation, form flows, verification (moved from Opus, 5.5x savings)
8. **Requirements-Checker (Sonnet)**: Post-implementation validation (moved from Opus)

**Key migration**: Shifted 5 agent roles from Opus to Sonnet based on benchmark data

### Cost Model Evolution

The 1.6x cost differential between models (4.6 generation) vs 5x (4.0 generation) changes routing economics:

- **Output pricing dominates browser automation costs**: Where output is high-volume, Opus's premium amplifies dramatically (5.5x for browser QA)
- **Token efficiency doesn't equal quality gains**: Sonnet's faster tool calling and lower output tokens partially offset capability gaps
- **Depth-first reasoning has distinct pricing floor**: Multi-hop architectural analysis shows diminishing returns when scaled to 10+ sessions (justifies Opus only for truly exceptional cases)

## Implications

### For Architecture Decision-Making

1. **End the "always Opus" pattern**: Model selection must be task-specific, not blanket cost-minimization or blanket capability assumptions
2. **Adversarial/breadth analysis now favors Sonnet**: The model that casts a wider net across edge cases and security patterns outperforms the flagship on this workload
3. **Depth-first reasoning remains Opus territory**: Only when multi-layer semantic relationships matter—architectural tracing, complex error propagation, novel system design
4. **Output-heavy workloads are cost cliffs**: Browser automation, data transformation pipelines, or any agentic task generating large outputs should default to Sonnet unless Opus adds provable value

### For Agentic Workflow Design

1. **Deterministic tasks (pattern scanning, rule enforcement, parsing)**: Sonnet proves adequate; consider Haiku for highest throughput on mechanical tool calls
2. **Self-healing loops**: Sonnet's faster iteration and lower cost create economic incentive to run more correction cycles rather than hoping Opus gets it right first time
3. **Tool-calling efficiency**: Sonnet's improved tool-call metrics directly apply to agentic work; the benchmark shows this translates to real-world win rates, not just synthetic benchmarks
4. **Parallel agent workflows**: Complement Sonnet's breadth findings with occasional Opus deep-traces rather than default to Opus everywhere (cuts cost ~60% with minimal quality loss)

### For Cost Optimization at Scale

- **10 browser tests @ Sonnet = $2.40 vs Opus = $13.20**: 5.5x leverage through model selection alone
- **PR review at scale**: Sonnet for breadth phase (9 issues found) + Opus spot-check for architectural risk (when flagged by architect agent) = ~1.3x Sonnet cost while capturing 11 total issues
- **Token-insensitive budgets still benefit**: Even 20x subscription teams see 30% pipeline cost reduction by removing unnecessary Opus usage (per author's separate workflow optimization)

## Sources

- [old.reddit.com/r/ClaudeAI/comments/1r9jf2j/i_benchmarked_opus_46_vs_sonnet_46_on_agentic_pr/](https://old.reddit.com/r/ClaudeAI/comments/1r9jf2j/i_benchmarked_opus_46_vs_sonnet_46_on_agentic_pr/) - Full post with agent configuration details
