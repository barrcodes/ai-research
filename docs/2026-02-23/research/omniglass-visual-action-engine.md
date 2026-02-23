# OmniGlass - An Open-Source, Sandboxed Visual Action Engine

| | |
|---|---|
| **Source** | GitHub - goshtasb/OmniGlass |
| **URL** | [github.com/goshtasb/OmniGlass](https://github.com/goshtasb/OmniGlass) |
| **Researched** | 2026-02-23 |

## Overview

OmniGlass bridges visual screen content and executable actions by coupling OCR with LLM classification to generate actionable menu items. Rather than a generic "screenshot analyzer," it's designed as a pluggable action engine where users snip screen regions, the system identifies what they're looking at, and immediately presents executable options. The architecture prioritizes security through kernel-level sandboxing and PII redaction before cloud model interaction.

## Key Points

- **Unified visual pipeline**: Native OCR (Vision API on macOS, Windows OCR) → LLM classification → sandboxed action execution eliminates context switching between tools
- **MCP-based plugin system**: Plugins receive structured JSON action payloads rather than free-form prompts, reducing prompt engineering overhead and improving determinism
- **Defense-in-depth security**: Kernel sandboxing, environment filtering, mandatory user confirmation, and PII redaction before cloud LLM calls—addresses the core risk of visual automation (sending sensitive screen content to models)
- **Multi-model flexibility**: Supports Claude Haiku (~3s), Gemini Flash (~3s), or offline Qwen-2.5-3B via llama.cpp (~6s), enabling latency/capability trade-offs
- **Production-ready on macOS**, with Windows builds compiling successfully but pending hardware validation

## Technical Details

**Architecture Pattern**: The system implements a three-stage processing pipeline:

1. **Input**: Screen region snipping or text query
2. **Classification**: On-device OCR → LLM-based content type detection and action recommendation
3. **Execution**: User-confirmed action routing through MCP plugins with environment isolation

**Plugin Design**: Rather than embedding LLM reasoning inside each plugin, OmniGlass centralizes classification at the engine level. Plugins operate as pure action handlers receiving structured JSON, eliminating redundant model calls and prompt divergence.

**Security Primitives**:
- macOS sandbox-exec for kernel isolation
- Environment variable scrubbing (hides AWS keys, API tokens, etc.)
- Mandatory confirmation dialogs before execution
- PII scrubbing on data destined for cloud models

**Latency Profile**: Haiku/Gemini Flash achieve ~3s end-to-end; offline Qwen-2.5-3B ~6s, enabling real-time copilot workflows without cloud dependency.

## Implications

**When to adopt**: Organizations building AI-driven desktop automation, multi-agent systems, or computer-use workflows where visual grounding matters. The sandboxing model addresses the legitimate security concerns around LLM-to-action automation.

**Architectural insight**: The separation of LLM classification (centralized) from action execution (pluggable) is a reusable pattern for reducing hallucination risk in agent systems. Plugins can't invent actions; they can only execute pre-validated operations.

**Trade-offs**:
- Requires native OCR support per platform (currently macOS-first)
- User confirmation flow may slow automation for trusted actions (solvable with permission scopes)
- Offline-first path via Qwen trades latency (~6s) for privacy and cost

**Competitive positioning**: Unlike general computer-use agents (which reason over entire screens), OmniGlass assumes user-directed snipping—higher precision, lower automation scope, but stronger security posture. Viable for enterprise copilots where safety governance matters.

## Sources

- [github.com/goshtasb/OmniGlass](https://github.com/goshtasb/OmniGlass) - Open-source repository with architecture documentation, security model, and plugin examples
