# Claudebin - Share and Resume Claude Code Sessions

| | |
|---|---|
| **Source** | Claudebin |
| **URL** | [claudebin.com](https://claudebin.com/) |
| **Researched** | 2026-02-19 |

## Overview

Claudebin solves a critical pain point in AI-assisted development: capturing and sharing executable Claude Code sessions as reproducible, embeddable artifacts. It converts complete session logs—including prompts, responses, tool invocations, file paths, and execution context—into publicly shareable threads that preserve the full interaction history and allow direct continuation from the UI.

## Key Points

- **Session capture & continuability**: Full conversation state persists, enabling users to resume sessions mid-workflow with context intact rather than restarting from scratch
- **Flexible sharing controls**: Public discoverable threads, unlisted (link-only) threads, and embeddable snippets address different sharing scenarios without forcing public exposure
- **Community-focused discovery**: Featured threads, search, user profiles, and engagement metrics (view counts, likes) create discoverability patterns similar to Gists or CodePen
- **Open-source & lightweight**: Free platform with no apparent premium tier removes friction for adoption and experimentation
- **GitHub-native authentication**: Leverages existing developer identity and OAuth, reducing signup friction

## Technical Details

**Architecture Stack:**
- Supabase (PostgreSQL + auth + object storage)
- Vercel (serverless hosting + analytics)
- Cloudflare R2 (video asset CDN)
- OpenRouter integration (GPT-4o-mini for automatic title generation)

**Captured Session Data:**
- User prompts and Claude responses
- Tool execution results (Read, Write, bash outputs)
- File paths and working directory state
- Timestamps and model metadata
- Complete execution context for reproducibility

Server-side authentication ensures only authorized users can store sessions while maintaining secure access to captured artifacts.

## Implications

**For practitioners:**
- **Knowledge capture at scale**: Enables documenting complex multi-step Claude Code workflows without manual transcription—valuable for onboarding, debugging, or sharing architecture decisions
- **Reduced context loss**: Instead of copying snippets or screenshots, preserve the full reasoning chain and tool interactions that led to solutions
- **Teaching & code review leverage**: Reviewers and mentees can trace exact steps, rerun segments, and continue conversations—stronger than static documentation
- **Risk consideration**: Public threads reveal full session history; sensitive data in prompts/outputs requires careful curation before sharing

**Architectural trade-offs:**
- Single auth provider (GitHub) creates platform lock-in but dramatically simplifies implementation
- Supabase + Vercel commodity stack ensures easy self-hosting if needed
- Real-time continuation requires maintaining session state server-side; not suitable for long-term "frozen" snapshots

**When to use:**
- Documenting Claude Code-driven architecture decisions and explorations
- Collaborative code review where reviewers need interactive access, not just diffs
- Building living examples or tutorials for AI-assisted development patterns
- Knowledge bases for team onboarding in AI-native workflows

## Sources

- [Claudebin](https://claudebin.com/) - Free platform for sharing and resuming Claude Code sessions
