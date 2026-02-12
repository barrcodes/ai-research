# Warcraft III Peon Voice Notifications for Claude Code

| | |
|---|---|
| **Source** | peon-ping project |
| **URL** | [peon-ping.vercel.app](https://peon-ping.vercel.app/) |
| **Researched** | 2026-02-12 |

## Overview

peon-ping is a Claude Code notification system that solves a fundamental workflow friction point: developers lose context when Claude Code finishes a task or requires permission but provides no audible notification. By mapping Claude Code lifecycle events to Warcraft III unit voice lines, the tool maintains developer flow through audio signaling while maintaining terminal awareness via status messages and tab titles.

## Key Points

- **Event Mapping**: Hooks into Claude Code's SessionStart, UserPromptSubmit, Stop, and Notification events, mapping each to contextual voice categories (greeting, acknowledge, complete, error, annoyed)
- **Cross-Platform Audio**: Implements platform-specific playback (afplay for macOS, PowerShell MediaPlayer for WSL2), ensuring compatibility across developer environments
- **Smart Voice Selection**: Maintains per-category sound history to prevent audio repetition—critical for sustained attention in long coding sessions where the same line twice in succession breaks immersion
- **Configurable Sound Packs**: Multiple character voice sets available (Orc Peon, Human Peasant, Soviet Engineer, StarCraft units), enabling community contribution and personal preference
- **UI Integration**: Updates Terminal tab titles with project name and status indicator, providing both visual and auditory feedback channels
- **Volume Control**: Configurable output levels (0.0–1.0) tuned for office environments where ambient noise management matters

## Technical Details

**Installation**: Single-line bash curl installation adds hook configuration to Claude Code setup.

**Architecture**: Shell script registered with Claude Code's event system acts as state machine, tracking:
- Last played sound per category (prevents repetition)
- Current project context (for tab title updates)
- Configuration state (volume, enabled categories)

**Sound Design**: Voice lines are short (1–3 seconds), thematically coherent, and calibrated to interrupt sustained focus without jarring. The Warcraft III/StarCraft aesthetic provides consistent, recognizable audio markers that developers can mentally map to action states.

**Extensibility**: Pack-based system with straightforward manifest + audio file structure enables community contributions. Contributing a new character voice pack requires:
1. Audio files organized in `packs/<name>/sounds/`
2. Manifest defining sound mappings to categories
3. Pull request to main repository

## Implications

**For Individual Developers**: Eliminates "babysitting" behavior where developers remain context-bound to terminal output waiting for task completion signals. Enables genuine context switching (Slack, documentation, code review) while maintaining awareness of Claude Code state through ambient audio.

**For Workflow Optimization**: Demonstrates that solving seemingly trivial UX friction in IDE/agent interaction can compound into significant productivity gains. The 5–10 second installation barrier is removed—adoption friction is intentionally minimized.

**For Tool Integration Philosophy**: Shows the value of hooking into low-level event systems (Claude Code's SessionStart/Stop/Notification model) rather than expecting users to implement custom integrations. The tool works with "any Claude Code setup on macOS," indicating platform-agnostic event abstraction exists.

**For Developer Tooling**: Illustrates that thoughtful notification design matters in agentic workflows. Unlike traditional CI/CD webhooks, which often default to Slack integration, this approach prioritizes immediate developer awareness through the primary work interface (terminal/IDE).

## Sources

- [peon-ping.vercel.app](https://peon-ping.vercel.app/) - Official project website with installation instructions and sound pack browser
- [github.com/tonyyont/peon-ping](https://github.com/tonyyont/peon-ping) - Source repository with implementation and pack system
- [Hacker News discussion](https://news.ycombinator.com/item?id=46967773) - Community reception and use case discussion
