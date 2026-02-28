# Claude-File-Recovery

| | |
|---|---|
| **Source** | GitHub - hjtenklooster/claude-file-recovery |
| **URL** | [github.com/hjtenklooster/claude-file-recovery](https://github.com/hjtenklooster/claude-file-recovery) |
| **Researched** | 2026-02-27 |

## Overview

Claude-File-Recovery reconstructs files created or modified during Claude Code sessions by parsing JSONL session transcripts stored in `~/.claude/projects/`. The tool provides point-in-time recovery with a performant interactive terminal UI, enabling developers to retrieve lost work, audit code changes, and batch-extract files from development sessions.

## Key Points

- **Session replay**: Reconstructs file contents at any point in time by replaying Write, Edit, and Read operations chronologically across multiple sessions
- **Interactive TUI**: Vim-style navigation with fuzzy search, multiple diff modes (unified/full-context/raw), and multi-file selection for batch extraction
- **High-performance parsing**: Parallel JSONL processing with byte-level filtering that skips ~77% of non-relevant lines before JSON parsing
- **Point-in-time queries**: Binary search support for time-based recovery with timestamps and filtering (`--before` flag)
- **Symlink deduplication**: Intelligent path canonicalization for cleaner file recovery across linked paths

## Technical Details

**Architecture (4-step pipeline):**

1. **Scan** — Parallel thread-pooled discovery and parsing of JSONL transcripts with fast-reject byte checks
2. **Correlate** — Matches tool requests to results via `tool_use_id` identifiers to attach file content
3. **Reconstruct** — Replays file operations in chronological order per path with binary search for time-based snapshots
4. **Present** — Interactive TUI with search, diff, selection, and extraction capabilities

**Installation**: Python 3.10+ via `uv tool install claude-file-recovery` (recommended), `pipx install`, or `pip install`

**Key commands**:
- `claude-file-recovery` — Launch interactive TUI
- `claude-file-recovery list-files [--filter '*.py'] [--before '2025-02-20 14:00']`
- `claude-file-recovery extract-files --output ./recovered [--filter '*.py']`

**TUI Keybindings**: j/k (navigate), g/G (jump), / (search), x/Space (select), d (cycle diffs), Ctrl+E (extract), Ctrl+R (search mode)

## Implications

For architects and senior engineers, this tool addresses a practical gap in AI-assisted development workflows: capturing the full audit trail of agent-generated code. Rather than relying on git history alone, it provides session-level granularity for reviewing Claude Code's output, comparing drafts, and recovering work lost between commits. The performance optimizations (parallel parsing, byte-level filtering) make it viable for large projects with thousands of operations. Use cases include code review workflows, compliance auditing of AI-generated changes, and recovery from interrupted sessions. The design trades simplicity for power—it's a targeted tool for Claude Code users rather than a general-purpose recovery utility.

## Sources

- [hjtenklooster/claude-file-recovery](https://github.com/hjtenklooster/claude-file-recovery) - GitHub repository with interactive TUI for session recovery
