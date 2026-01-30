# native-devtools-mcp: MCP Server for Desktop Application Testing

| | |
|---|---|
| **Source** | GitHub Repository |
| **URL** | [github.com/sh3ll3x3c/native-devtools-mcp](https://github.com/sh3ll3x3c/native-devtools-mcp) |
| **Researched** | 2026-01-26 |

## Overview

native-devtools-mcp is a Model Context Protocol (MCP) server that gives Claude AI agents "eyes and hands" to interact with native desktop applications. The server enables automated testing and interaction across macOS and Windows through computer vision, OCR, and platform-specific input simulation—with all processing happening locally, no screenshots or data sent externally.

## Key Points

- **Two interaction modes**: Visual mode works with 99% of applications (Electron, Qt, browsers, games) via screenshot analysis and coordinate-based clicking; Structural mode uses AppDebugKit for deep DOM-like inspection of instrumented applications
- **Platform-native implementation**: Rust + Swift backend leveraging CoreGraphics/Vision (macOS) and GDI/Win32/WinRT (Windows) APIs directly
- **No external dependencies**: Screenshots, OCR, and control operations execute locally with zero cloud transmission
- **Essential permissions model**: macOS requires Screen Recording and Accessibility grants; Windows operates with fewer restrictions
- **Direct JSON-RPC communication**: Claude client communicates with local MCP server, which mediates all system API calls

## Technical Details

| Component | macOS | Windows |
|-----------|-------|---------|
| Screenshots | CoreGraphics | GDI BitBlt |
| Text Recognition | Vision Framework | WinRT Media.Ocr |
| Input Simulation | Native APIs | Win32 SendInput |

The 78% Rust codebase with 21% Swift handles platform abstraction. Distribution options include npm (`npx native-devtools-mcp`), global installation, or source builds. Window management APIs enumerate open applications and allow focus-switching for targeted interaction.

## Implications

**For test automation practitioners**: This shifts desktop testing from brittle coordinate-based or fragile accessibility tree parsing to a more flexible vision-based approach—similar to how humans test UI. The visual mode handles unmodified applications without instrumentation burden, lowering adoption friction.

**Architecture trade-off**: Visual mode sacrifices precision (pixel-coordinate based) for universality (works on anything rendered). Structural mode sacrifices universality for precision (requires app-level instrumentation). Choose visual for third-party app testing, structural for deeply integrated testing of your own applications.

**Operational constraint**: Agents require exclusive input control during execution—concurrent manual input creates race conditions and unpredictable behavior. This necessitates clear user SLAs and possibly hardware isolation for production automation.

**Privacy advantage**: All processing is local, removing data egress concerns that plague cloud-based testing solutions. Relevant for regulated environments testing sensitive applications.

## Related

- [Model Context Protocol](https://modelcontextprotocol.io/) - The protocol foundation enabling this integration
- [AppDebugKit](https://developer.apple.com/documentation/) - Apple's framework for structural UI inspection used in instrumented mode
- Electron/Qt/Web accessibility APIs - Alternative approaches for specific application frameworks
