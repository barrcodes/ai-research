# Unwrapping Claude Code's Black Box: claude-devtools for CLI Observability

| | |
|---|---|
| **Source** | Reddit - r/ClaudeAI |
| **URL** | [old.reddit.com/r/ClaudeAI/comments/1r90pol/](https://old.reddit.com/r/ClaudeAI/comments/1r90pol/claude_codes_cli_feels_like_a_black_box_i_built_a/) |
| **Researched** | 2026-02-20 |

## Overview

A developer (MoneyJob3229) built `claude-devtools`, a free, open-source desktop app that provides transparent visibility into Claude Code CLI execution by passively tailing local session logs. The tool gained rapid traction (700+ GitHub stars, 3.7k downloads, #3 on Hacker News within days) by solving a critical pain point: the Claude Code CLI operates as a "black box," providing minimal observability into token consumption, file operations, and agent decision trees. Critically, claude-devtools is not a wrapper—it imposes zero token overhead and maintains native terminal workflow compatibility.

## Key Points

- **The Problem**: Official Claude Code CLI hides execution details. Developers can't see what files are being edited, whether critical files like `.env` are accessed, dependency hallucinations, or why context windows fill rapidly. Alternatives (other GUIs, `--verbose mode`) either sacrifice terminal workflow history or produce illegible interleaved logs, especially with parallel agents.

- **The Solution Architecture**: claude-devtools reads the proprietary `.jsonl` log format from `~/.claude/` directory and reconstructs execution traces without intercepting commands or injecting prompts. It's a pure passive observer with zero token overhead, fundamental to its value proposition.

- **Primary Features**:
  - **Context Forensics**: Token breakdown by File Content vs. Tool Output vs. Thinking vs. CLAUDE.md, identifying which massive files or MCP outputs bankrupt the context window
  - **Agent Trees**: Visualizes parallel Teams execution and sub-agent hierarchies in readable tree format, untangling the mess of verbose logs
  - **Custom Triggers & Notifications**: OS notifications for suspicious activities (e.g., .env file reads, single file reads exceeding 4k tokens)
  - **Real Inline Diffs**: Shows exact additions/deletions per file instead of generic "Edited 2 files" summaries

- **Compatibility**: Works with VS Code Claude Code extension (same logs), supports multi-tab sessions (frontend/backend separation), beta support for remote SSH. Does NOT work with GitHub Codespaces, Amazon Kiro, or web-based Claude Chat/Co-work.

## Technical Details

### Log Architecture
The tool reverse-engineers Claude Code's `.jsonl` session log format. Unlike wrapper tools that only see commands through their own UI, claude-devtools reconstructs all terminal history because it reads the authoritative source logs directly from disk. This design choice eliminates context switching penalties and session loss.

### Token Visibility Limitations
The tool cannot see "dark matter" context: base system prompts, built-in tool schemas, auto-injected CLAUDE.md traces, and periodic system reminders that the CLI keeps in-memory only. By design, it focuses on *visible context*—the factors developers can actually control and optimize.

### Performance Considerations
For remote SSH connections, the tool uses SFTP streaming with LRU caching (50 sessions/10min) but is limited by polling (3s intervals) since `fs.watch` doesn't work over SFTP. The author is exploring smarter polling strategies and remote `inotifywait` spawning for optimization.

## Four Token Optimization Patterns (From Log Analysis)

1. **Heavy MCPs & Large Files**: Tools like `typescript-lsp-mcp` returning 10k+ tokens in a single call cause Claude to degrade significantly. Visualizing this prompted codebase refactoring into leaner files and `.claudeignore` expansions.

2. **Lazy File Mentions**: Not explicitly `@`-mentioning files forces Claude to use Grep/Read tools to hunt for context, wasting tokens. Direct file pinpointing loads context without tool calls, improving task completion.

3. **Automatic Skills**: Dynamically finding and invoking custom skills is hit-or-miss and token-inefficient. Explicit skill instruction from the start performs better.

4. **Layered CLAUDE.md Architecture**: Monolithic CLAUDE.md files drain context on every turn. Directory-specific or layered instructions keep context localized and efficient.

The value isn't inventing new best practices but making their token impact *visible and measurable* in real sessions—"seeing the drain live hits different" than reading best practices.

## Implications

**For Practitioners:**

- **Operational Visibility**: Claude Code adoption in production/team settings requires observability equivalent to server debugging. claude-devtools fills this gap for the CLI specifically, enabling confidence in parallel agent deployments and large-scale automation.

- **Context Engineering as a Discipline**: With token breakdown visualization, context optimization shifts from folklore ("don't use huge files") to data-driven decisions. Teams running multiple CC sessions can quantify token waste sources.

- **Complementary, Not Competing**: As a passive log reader, not a wrapper, this doesn't fragment the Claude Code ecosystem. Works alongside native terminal workflows, VS Code extensions, and supports self-hosting via Docker.

- **Architectural Blind Spot**: The lack of official observability tools for Claude Code suggests an opportunity for Anthropic to either build this natively or standardize log formats. The rapid adoption signals that observability-blind AI automation is a genuine blocker for enterprise adoption.

- **Design System Accessibility**: The post includes detailed design system documentation in the GitHub repo (`.claude/commands/ccc/design-system.md`), demonstrating that modern AI-assisted tooling can be built and documented systematically, not ad-hoc.

## Sources

- [GitHub - claude-devtools](https://github.com/matt1398/claude-devtools) - Official MIT-licensed repository with design system and Docker self-hosting docs
- [claude-dev.tools](https://claude-dev.tools) - Official marketing website with feature overview
- [Original Post in r/ClaudeCode](https://www.reddit.com/r/ClaudeCode/comments/1r3to9f/claude_codes_cli_feels_like_a_black_box_now_i/) - Earlier discussion that reached #3 on Hacker News
