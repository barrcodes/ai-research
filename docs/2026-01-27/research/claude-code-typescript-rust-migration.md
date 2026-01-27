---
title: Porting 100k Lines from TypeScript to Rust Using Claude Code in a Month
source: Hacker News
url: https://news.ycombinator.com/item?id=46765694
researched_at: 2026-01-27T00:00:00Z
---

# Porting 100k Lines from TypeScript to Rust Using Claude Code in a Month

## Overview

A developer successfully translated 100,000 lines of TypeScript (Pokémon Showdown battle simulator) to Rust in ~30 days using Claude Code as the primary implementation engine, achieving 3.5x performance improvement. The project demonstrates both the scalability of LLM-driven code migration and critical limitations around code quality, maintenance, and correctness when bypassing human review at scale.

## Key Points

- **Differential fuzzing validation**: 2.3 million random battle simulations achieved 99.96% pass rate against original TypeScript, proving functional equivalence despite lack of human code review
- **Autonomous workflow automation**: AppleScript wrapper auto-confirmed prompts, effectively running Claude unattended—trades supervision for velocity
- **File-by-file > holistic porting**: Breaking work into granular chunks outperformed whole-codebase approaches; improved focus and error isolation
- **Model behavior issues**: Claude repeatedly attempted "improvements" that introduced bugs, duplicated data structures across files, and lost architectural context mid-project
- **Performance optimization failed**: All attempted optimizations degraded runtime or broke functionality; final 3.5x speedup came entirely from language translation, not tuning
- **Zero domain expertise required**: Author had no prior Rust experience; entire implementation relied on Claude's code generation without ability to audit generated patterns

## Technical Details

**Architecture challenges**:
- Context window management caused Claude to "recap" and lose established patterns during long sessions
- Generated code contains non-idiomatic Rust patterns (GC-language translation artifacts)
- Docker configuration and deployment instructions remained incomplete—infrastructure work bypassed

**Testing methodology**:
- Property-based approach comparing battle simulation outputs
- Edge cases remain unimplemented despite high pass rate
- No human code review; correctness depends entirely on test coverage

**Build and iteration**:
- Iterative prompt refinement to prevent unwanted "improvements"
- Explicit constraints in prompts more effective than implicit expectations
- Compilation and test feedback loops accelerated debugging

## Implications

**When this pattern works**:
- Well-tested legacy codebases with comprehensive existing test suites
- Structured, deterministic business logic (battle simulators, calculators, game engines)
- Performance bottlenecks from language, not algorithm—Rust's zero-cost abstractions provide immediate wins

**Critical trade-offs**:
- Speed vs. maintainability: 30-day delivery sacrifices long-term code quality and developer understanding
- Unaudited patterns create technical debt—code works but doesn't reflect Rust idioms or best practices
- Context blindness: LLMs lose architectural vision at scale; file-by-file work fragments design coherence
- Incomplete implementation: Supporting infrastructure (deployment, configuration) requires manual work

**Architectural lessons**:
1. LLM-driven migration excels at syntax translation, fails at semantic optimization
2. Comprehensive differential testing (not code review) validates functional correctness
3. Breaking large projects into <5k-line chunks improves model focus and accuracy
4. The "moving fast" vs. "moving slow" decision shifts entirely based on maintenance burden vs. time-to-market

**What's not solved**: This approach doesn't address maintainability, knowledge transfer, or idiomatic patterns. The result is production-ready test-validated code that no human has reviewed—suitable for performance-critical compute but risky for business logic where intent matters.

## Related

- [Pokémon Showdown repository](https://github.com/smogon/pokemon-showdown) - Original TypeScript codebase
- [Claude Code documentation](https://claude.ai/help) - Tool capabilities and limitations
- [Differential fuzzing](https://en.wikipedia.org/wiki/Fuzzing) - Testing methodology used for validation
