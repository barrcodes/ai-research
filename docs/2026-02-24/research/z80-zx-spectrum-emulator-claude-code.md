# Implementing a Z80/ZX Spectrum Emulator with Claude Code

| | |
|---|---|
| **Source** | antirez.com |
| **URL** | [antirez.com/news/160](https://antirez.com/news/160) |
| **Researched** | 2026-02-24 |

## Overview

Antirez documented a "clean room" experiment where Claude Code generated a complete Z80 processor emulator and ZX Spectrum computer simulator from structured specifications alone—no existing source code, no internet access during implementation. The result: 1,200 lines of C handling all official and unofficial Z80 instructions, passing ZEXDOC/ZEXALL test suites, and running Jetpac at 8% CPU overhead on commodity hardware.

## Key Points

- **Clean room methodology works**: Claude Code produced functionally correct, original low-level systems code when given detailed specifications (markdown design docs + technical references) but zero reference implementations. This proves AI agents can execute complex systems work without "learning from" existing code.

- **Instruction-level execution, not cycle-by-cycle**: Architectural decision to execute complete instructions atomically rather than simulate single clock cycles enabled resource-constrained deployment (RP2350 embedded systems). Clock cycle tracking added for future contention modeling without instruction-level simulation overhead.

- **Documentation-driven beats minimal steering**: Providing comprehensive design specifications upfront (architectural constraints, instruction classes, memory layout, timing requirements) outperformed zero-guidance or minimal-prompt approaches. TAP cassette loading required human clarification around timing expectations—areas where behavioral testing alone proved insufficient for agent verification.

- **Hybrid workflow for refactoring**: Agent-generated implementations benefited from tactical human guidance on architectural improvements (e.g., separating `zx_tick()` from `zx_frame()`), accelerating convergence versus passive observation.

## Technical Details

**Emulation architecture**: Full instruction execution model with optional RGB framebuffer rendering for direct scanline output to embedded displays. ROM stored as const data rather than heap-copied, minimizing footprint. All Z80 addressing modes and unofficial instructions (IXCB prefixes, undocumented flags) implemented.

**Test verification**: ZEXDOC and ZEXALL benchmark suites validated instruction correctness. Jetpac cassette loading tested timing-critical features with TAP file support and proper LOAD synchronization.

**Code metrics**: ~1,200 lines (1,800 with comments) for full Z80 core; Spectrum implementation added ULA/AY-3-8912 sound, memory banking, and ROM management. Single-core CPU usage 8% during gameplay at full performance.

## Implications

For AI-assisted systems development, this demonstrates that **specification quality directly gates implementation quality**. Writing detailed markdown design docs (state machine rules, register encodings, timing constraints) before agent work is foundational—LLMs excel at systematic transformation of well-defined constraints into code, but struggle with implicit domain knowledge or verification-by-observation.

The clean room approach also surfaces when **human steering becomes necessary**: agents autonomously handled instruction sets and instruction decoding but required clarification on timing-dependent behavior (cassette load synchronization). This suggests a hybrid workflow for systems code: detailed specs + autonomous implementation + targeted human review on timing/concurrency concerns.

The instruction-level execution trade-off is architecturally significant: prioritizing portability to microcontrollers over cycle-accurate simulation is a valid design choice for emulation, but requires explicit documentation to agents about this constraint and its implications for feature sequencing.

## Sources

- [antirez.com/news/160](https://antirez.com/news/160) - Original article documenting Z80/ZX Spectrum emulator implementation with Claude Code
