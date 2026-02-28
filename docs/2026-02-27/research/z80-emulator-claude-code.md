# Implementing a Z80 / ZX Spectrum Emulator with Claude Code

| | |
|---|---|
| **Source** | antirez.com |
| **URL** | [antirez.com/news/160](https://antirez.com/news/160) |
| **Researched** | 2026-02-27 |

## Overview

Antirez (Redis creator) successfully implemented a Z80/ZX Spectrum emulator in 1200 lines of well-commented C code using Claude Code, completing the core instruction set in 20-30 minutes. The experiment demonstrates that frontier LLMs excel at systems programming when provided structured documentation and iterative testing feedback, but struggle with timing-sensitive behavior without interactive debugging capabilities.

## Key Points

- **Documentation-driven development outperforms minimal guidance**: Comprehensive markdown specs defining scope, requirements, and implementation rules generated cleaner, production-ready code than vague instructions. This inverts the typical assumption that LLMs work best with minimal constraints.

- **Iterative testing beats monolithic generation**: Claude Code implemented instruction classes incrementally while validating against ZEXDOC/ZEXALL test binaries. This closely mirrors human programmer workflows (build-test-iterate) rather than generate-once patterns.

- **Clean room prevents memorization bias**: Prohibiting internet access while providing curated technical specifications and test vectors prevented the agent from reproducing memorized implementations. The resulting code shows genuine pattern synthesis, not regurgitation.

- **Steering matters for timing-dependent systems**: The TAP file loading required "extensive steering"—refactoring abstractions to expose tick-level control so the agent could reason about synchronization. LLMs struggle observing runtime behavior (border changes, state transitions) without interactive feedback.

- **Context reset boundaries become workflow inflection points**: Between major resets, the agent must re-read specifications. This creates natural checkpoints for human validation and course correction.

## Technical Details

The emulator targets the Z80 CPU and ZX Spectrum system. Claude Code:

- Implemented instruction dispatch and ALU operations in well-structured C
- Passed formal instruction tests (ZEXDOC/ZEXALL binaries)
- Struggled with cassette loading (TAP format) due to timing expectations and inability to observe state changes at frame boundaries

The key architectural insight: LLMs can synthesize low-level CPU behavior (instruction execution, flag computation) but need human intervention to model abstract timing contracts (cassette loader expectations, render pipeline synchronization).

## Implications

**For systems programming with AI**: Treat LLMs as amplifiers of human architects, not autonomous code generators. Invest time in specification documentation—it pays dividends in code quality. Timing-sensitive subsystems and I/O interactions remain human-steered domains; the agent excels at computation-heavy, testable logic.

**For prompt engineering**: Traditional "vibe coding" (describe goal, accept output) produces fragile, over-complex code. Instead, provide structured specifications like ISA docs, interface contracts, and test vectors. This mirrors how human engineers onboard to unfamiliar domains.

**Against memorization theories**: The agent doesn't emit copies of training data; it synthesizes novel patterns from known abstractions. This justifies licensing emulator code under MIT—genuine intellectual work, not regurgitation.

**For workflow design**: Human-in-the-loop iteration beats hands-off autonomy. Interactive debugging, state inspection, and refinement cycles are where LLMs currently deliver value. Unattended agents on non-trivial projects produce "fragile code bases that are larger than needed."

## Sources

- [antirez.com/news/160](https://antirez.com/news/160) - Full article on Z80 emulator implementation with Claude Code
