# UIUC 2002 - We Wrote a Space Shooter in x86 ASM. In 2026 Claude Resurrected It

| | |
|---|---|
| **Source** | GitHub Repository |
| **URL** | [github.com/gottebp/alan_parsons_project](https://github.com/gottebp/alan_parsons_project) |
| **Researched** | 2026-02-15 |

## Overview

A 24-year-old x86 assembly space shooter from a 2002 UIUC ECE291 course was modernized by Claude into a cross-platform C/WebAssembly implementation. The project demonstrates architectural principles for translating legacy low-level code into maintainable, multi-platform systems while preserving original game logic and adding new capabilities like mobile controls.

## Key Points

- **Legacy Transformation**: Claude converted raw x86 assembly (Middle Earth-themed particle space shooter) to clean C with explicit state passing and proper architectural layering, maintaining gameplay fidelity while eliminating platform coupling.

- **Multi-Platform Architecture**: Single C codebase builds natively via SDL2 (macOS/Linux) and to browser via Emscripten/WebAssembly, with platform abstraction separating game logic from rendering/audio concerns.

- **Gameplay Enhancements**: Modernization added body collision mechanics, mobile touch controls, and visual feedback (boss damage smoke) without disrupting the original six-level progression system with boss encounters and weapon progression.

## Technical Details

**Architecture Pattern**: The refactored codebase uses explicit state passing through Game and App structs, eliminating global state dependencies common in assembly code. Pool-based entity management with type-safe iteration macros enables efficient particle effects—core to the original particle-shooter concept.

**Build Matrix**:
```
Native:  make -f Makefile.c  →  alan_parsons executable (SDL2 dependency)
WASM:    ./build_wasm.sh    →  game.html (Emscripten SDK required)
```

**Audio Decoupling**: Rather than direct audio function calls embedded throughout logic, the system uses event flags to trigger sounds. This pattern prevents tight coupling and works across native/WASM platforms with different audio subsystems.

**Language Composition**: Project retains 36.4% of original x86 assembly (CPU-intensive routines), pairs with 54.5% new C code, and 7.3% HTML glue—pragmatic reuse rather than complete rewrite.

## Implications

**For Code Modernization**: This approach demonstrates that legacy assembly codebases needn't be abandoned or fully rewritten. Identify state-bearing components, introduce explicit structs, layer platform code separately, then migrate incrementally. The original authors could recognize their game logic intact.

**For Cross-Platform Strategy**: When targeting both native and browser, defer platform decisions to the edges (rendering, audio, input). WASM compilation constraints (no async I/O, limited syscalls) force this discipline—beneficial for all targets. Test WASM early; it exposes architectural coupling others hide.

**For AI-Assisted Modernization**: Claude's value here wasn't in generating boilerplate but in systematic refactoring: understanding 24-year-old assembly semantics, mapping to C equivalents, introducing layering without breaking behavior, and adding features that integrate cleanly. This pattern applies to modernizing proprietary legacy systems where domain knowledge exists but codebase structure doesn't match current practices.

## Sources

- [Alan Parsons Project Repository](https://github.com/gottebp/alan_parsons_project) - Complete source, build instructions, and game documentation
