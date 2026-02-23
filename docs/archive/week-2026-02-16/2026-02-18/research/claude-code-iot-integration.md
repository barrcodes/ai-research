# Claude Code: IoT Integration and Vibe Indicator

| | |
|---|---|
| **Source** | Reddit r/ClaudeAI |
| **URL** | [reddit.com/r/ClaudeAI/comments/1r783zr/](https://www.reddit.com/r/ClaudeAI/comments/1r783zr/turned_my_lamp_into_a_vibe_indicator_for_claude/) |
| **Researched** | 2026-02-18 |

## Overview

A developer (pbobak) integrated a Moonside Halo LED lamp with Claude Code using Bluetooth Low Energy (BLE) hooks to create a physical vibe indicator. The lamp changes states based on Claude Code's operational status: turning on/off with sessions, glowing pink when input is needed, and displaying blue animations during active processing. The implementation is open-sourced and uses Claude Code's hook system to bridge the AI agent's state with IoT hardware.

## Key Points

- **Hardware Integration**: Uses a Moonside Halo LED lamp with BLE connectivity, discovered and controlled via Python daemon communicating through Nordic UART Service
- **Hook-Based Architecture**: Integrates via Claude Code's hook system (`~/.claude/moonside_hooks/`), enabling per-project or global state triggering
- **State Management**: Three primary states—on/off (session lifecycle), pink (awaiting user input), blue animation (active tool use/processing)
- **Discovery Mechanism**: Lamp auto-discovers by name; no manual address configuration required
- **Portable Design**: Daemon monitors state file every 200ms, enabling placement anywhere within BLE range
- **Interoperability**: Developer verified compatibility across Moonside's product line via BLE standard

## Technical Details

**Architecture Overview:**
- Event Flow: Claude Code hook → `moonside_hook.sh` → `/tmp/moonside_state` → `moonside_daemon.py` ↔ BLE Connection ↔ Moonside Lamp
- **Hook Integration**: Shell script writes state on specific Claude Code hook events, spawning persistent daemon if needed
- **Daemon Process**: Python-based service that polls state file at 200ms intervals and maintains persistent BLE connection using Nordic UART Service
- **BLE Protocol**: Uses Bluetooth Low Energy for low-power, long-range wireless communication suitable for desktop environments
- **State Encoding**: Minimal serialization—daemon reads simple state file format and maps to lamp animation/color commands

**Installation Model:**
- Files placed in `~/.claude/` to leverage Claude Code's hook discovery
- Could be adapted for Claude Desktop via MCP (Model Context Protocol) wrapper
- Lamp auto-discovery eliminates configuration overhead for multi-device setups

## Implications

**For Practitioners:**

1. **Cognitive Load Reduction**: Physical ambient feedback removes the need to context-switch to check terminal/UI state, improving developer flow during long agentic sessions
2. **Hook System Extensibility**: Demonstrates Claude Code's hook system as a viable integration point for external hardware, opening pathways for custom monitoring tools, notification systems, and IoT coordination
3. **BLE as Integration Pattern**: Shows BLE's viability for developer tooling—auto-discovery and low power consumption make it suitable for desk-based monitoring without elaborate setup
4. **Template for IoT-Agent Integration**: Pattern applies beyond lamps—could extend to status displays, mechanical indicators, or multi-agent visualization systems

5. **MCP Compatibility**: While currently hook-based, porting to Claude Desktop via MCP Server wrapper would enable broader adoption and tighter integration with the Claude ecosystem

**Architectural Significance:**
- Validates the principle of "ambient computing" feedback for LLM-driven development—AI state should be perceivable without explicit polling
- Illustrates non-intrusive state exposure: the lamp acts as a passive observer rather than an interrupt mechanism, reducing cognitive friction
- Demonstrates productization potential: hardware-aware developer tooling remains underexplored; this shows clear UX benefits

## Sources

- [GitHub - bobek-balinek/claude-lamp: Claude hooks integration with Moonside lamps](https://github.com/bobek-balinek/claude-lamp) - Source repository with full implementation
- [Reddit r/ClaudeAI - Turned my lamp into a vibe indicator for Claude Code](https://www.reddit.com/r/ClaudeAI/comments/1r783zr/turned_my_lamp_into_a_vibe_indicator_for_claude/) - Original post with technical discussion
- [Moonside Halo Lamp](https://shop.moonside.design/products/moonside-halo-lamp) - Hardware product used in integration
