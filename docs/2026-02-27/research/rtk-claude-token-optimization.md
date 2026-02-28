# RTK: Rust Token Killer - CLI Proxy for Token Optimization

| | |
|---|---|
| **Source** | GitHub - rtk-ai/rtk |
| **URL** | [github.com/rtk-ai/rtk](https://github.com/rtk-ai/rtk) |
| **Researched** | 2026-02-27 |

## Overview

RTK is a lightweight CLI proxy that intercepts command output in Claude Code sessions and compresses it through intelligent filtering, grouping, and deduplication. It achieves 60-90% token reduction on common development commands—translating typical 150k-token sessions down to 45k tokens with zero dependencies as a single Rust binary.

## Key Points

- **Token efficiency**: Reduces per-session consumption by 70% on typical workflows; users report 89% compression on individual commands
- **Zero overhead**: Sub-10ms execution latency via transparent command rewriting; maintains development flow
- **Four-layer compression**: Smart filtering (removes boilerplate), grouping (aggregates similar items), truncation (preserves context), deduplication (collapses repeated entries with counts)
- **Cross-tool support**: Works with Claude Code, Cursor, Gemini CLI, Aider, Codex, Windsurf, and Cline
- **Concrete improvements**: `cargo test` (155→3 lines, 98% reduction), `git status` (119→28 chars, 76% reduction), test failure output (200+→20 lines)

## Technical Details

RTK integrates via PreToolUse hook in `~/.claude/settings.json`, rewriting commands transparently (e.g., `git status` → `rtk git status`). The proxy runs as a single stateless binary with zero external dependencies, optimizing for speed across macOS, Linux, and Windows.

Command processors are specialized per tool type:
- **Git**: Compresses logs, diffs, and status to essential changeset information
- **Testing**: Filters to failure summaries and relevant stack traces
- **Build systems**: Aggregates compiler/lint warnings by type with counts
- **File operations**: Groups directory listings by subdirectory with file counts

## Implications

For high-volume Claude Code users (especially architects iterating on complex codebases), RTK delivers tangible cost reduction and faster context cycling. The 70% token compression extends quota effectively 3x, allowing longer sessions or more iterative refinement passes on the same budget.

The <10ms latency constraint is deliberate—perceptible delays break developer flow and create cognitive friction. This makes RTK practical for always-on use without user friction.

Trade-off: Output compression may occasionally hide relevant context if filters are too aggressive. Teams should test on their dominant command patterns (git workflows differ between monorepos and service-oriented projects) before standardizing.

Best suited for: teams running long Claude Code sessions with verbose test suites or build output, monorepo workflows with expensive file operations, and cost-sensitive environments where quota utilization directly impacts iteration velocity.

## Sources

- [GitHub - rtk-ai/rtk](https://github.com/rtk-ai/rtk) - Main repository with CLI proxy implementation
- [rtk-ai.app](https://www.rtk-ai.app/) - Project website and documentation
- [GitHub Discussion: I saved 10M tokens (89%)](https://github.com/Kilo-Org/kilocode/discussions/5848) - Real-world usage report and benchmarks
