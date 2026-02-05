# Peen: A Minimal Coding Agent CLI Built for Local Models

| | |
|---|---|
| **Source** | GitHub - codazoda/peen |
| **URL** | [github.com/codazoda/peen](https://github.com/codazoda/peen) |
| **Researched** | 2026-02-05 |

## Overview

Peen is an experimental Node.js CLI that enables local LLMs (via Ollama) to function as autonomous coding agents. Unlike enterprise tools that enforce XML-based protocols, Peen adapts to how open-source models are actually trained—using JSON for tool communication. It trades sophistication for pragmatism: minimal dependencies, local-first design, and compatibility with off-the-shelf models like Qwen2.5-coder.

## Key Points

- **JSON-first protocol**: Avoids XML constraints; works with how local models naturally output tool calls
- **Ollama-based**: Runs models locally via HTTP, eliminating cloud dependencies and API costs
- **Command subset**: Supports `cat`, `ls`, `grep`, `echo` with basic safety guardrails—intentionally limited
- **Zero-build distribution**: Node.js CLI pulls raw files directly from GitHub on install
- **Environment-driven config**: `PEEN_HOST`, `PEEN_MODEL`, and optional temperature/context settings

## Technical Details

Peen operates as a simple request-response loop: user prompt → model streamed response → single-line JSON tool calls → CLI execution → context feeding. The implementation prioritizes compatibility over completeness. Configuration is minimal:

```
PEEN_HOST=http://localhost:11434  # Ollama endpoint
PEEN_MODEL=qwen2.5-coder          # Or qwen3-coder for GPU systems
PEEN_TEMPERATURE=0.7              # Optional
PEEN_CONTEXT_LENGTH=8000          # Optional
```

The tool self-updates on startup by checking GitHub, ensuring users stay current without manual intervention.

## Implications

**When to use**: Developers needing autonomous coding assistance without cloud lock-in; teams wanting to understand agentic patterns without enterprise complexity; local-first development environments.

**Trade-offs**: Limited command set trades capability for safety and simplicity; JSON protocol requires models trained on tool-use patterns; performance depends on local hardware and model capacity (typically 7-13B parameter models).

**Architectural significance**: Demonstrates that XML protocols (standard in enterprise tools) aren't necessary for agent communication—local models have different training data and constraints. Highlights the gap between cloud-native agent design and practical local LLM deployment.

## Sources

- [codazoda/peen](https://github.com/codazoda/peen) - Official repository and implementation
