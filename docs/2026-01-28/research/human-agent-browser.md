---
title: One Human + One Agent = Browser in 20K LOC
source: emsh.cat
url: https://emsh.cat/one-human-one-agent-one-browser/
researched_at: 2026-01-28T00:00:00Z
---

# One Human + One Agent = Browser in 20K LOC

## Overview

A developer built a functional cross-platform browser in 20,150 lines of Rust using a single persistent AI agent (Codex) through iterative human direction, not autonomous parallelization. The project challenges the scaling assumption that more agents equal better outcomes and demonstrates that continuity, human coordination, and tight feedback loops outperform distributed autonomous approaches for complex software engineering.

## Key Points

- **Single agent > many agents**: Persistent focus on one codebase enabled deeper architectural understanding and coherence compared to parallel multi-agent approaches
- **Tight feedback loops matter**: Iterative screenshot validation created fast, concrete feedback without needing formal test suites
- **Constraints drive quality**: Zero external dependencies and multi-platform support requirements forced better architectural decisions
- **Human judgment as coordinator**: Developer acted as decision-maker and quality gate—the human directed which features to build and validated results, not autonomous agents
- **Incremental validation pattern**: "Share screenshot, ask agent to replicate it" workflow maintained alignment without extensive specification overhead

## Technical Details

**Architecture**: 72 Rust files, <1000 LOC per file, three rendering backends:
- X11/Cairo (Linux)
- Direct2D/DirectWrite (Windows)
- Native APIs (macOS)

**Workflow**: Human-agent collaboration centered on visual parity testing. Developer captured screenshots of target websites, submitted them to the agent for implementation, validated output visually, and iteratively refined. This created a concrete, checkable specification without formal requirements documentation.

**Constraint-driven design**: No external dependencies forced the developer and agent to reason about system primitives and cross-platform compatibility from first principles, improving code quality and maintainability.

## Implications

For practitioners building AI-assisted tooling:

- **Resist the multi-agent default**: More parallel agents create coordination overhead and context fragmentation. A single focused agent with strong human direction may ship better results faster.
- **Build tight feedback loops**: Screenshot/visual validation is concrete and rapid. Use it instead of extensive upfront specifications.
- **Constraints improve outcomes**: Deliberate restrictions (no deps, multi-platform) prevented over-engineering and forced cleaner abstractions.
- **Human role is coordination**: Don't aim for fully autonomous agents; humans excel at high-level direction, validation, and architectural decisions. Pair that with agent implementation.

For agentic systems design, this challenges the scaling narrative and suggests that **continuity + tight feedback + human judgment** is the right model for complex, coherent systems—not autonomy at scale.

## Related

- [Agentic Systems Design](https://anthropic.com/research/introducing-claude-with-extended-thinking) - Context on agent coordination patterns
- [Rust FFI Patterns](https://doc.rust-lang.org/nomicon/ffi.html) - Cross-platform integration techniques
