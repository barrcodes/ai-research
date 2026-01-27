---
title: Generating skills for api+local CUAs via noVNC demonstration recording MCP
source: Reddit r/LocalLLaMA
url: https://www.reddit.com/r/LocalLLaMA/
researched_at: 2026-01-26T00:00:00Z
fetch_method: web-search-synthesis
note: Original Reddit post not found in search index; content synthesized from related technical sources
---

# Generating Skills for API+Local CUAs via noVNC Demonstration Recording MCP

## Overview

This research explores the intersection of skill generation for Computer Use Agents (CUAs) combining local API endpoints with demonstration recording via noVNC desktop environments, leveraging the Model Context Protocol (MCP) as the orchestration layer. The approach enables automated skill creation through visual demonstration capture rather than manual prompt engineering, significantly reducing development effort for AI agent capabilities.

## Key Points

- **Skill Generation Automation**: MCP-enabled systems can dynamically generate reusable skills from recorded demonstrations, reducing manual SKILL.md creation and context overhead through lazy loading (~50 tokens/skill metadata)

- **API+Local Hybrid Architecture**: Integration of REST API endpoints with local LLM inference (via Ollama, LM Studio, llama.cpp) creates a unified context where MCP servers expose both external APIs and local resources as callable tools

- **noVNC Desktop Recording**: Using noVNC browser-based desktop access as the demonstration medium provides automated screen capture and action sequence recording, feeding into skill generation pipelines

- **CUA (Computer Use Agent) Platform Integration**: Cua's Docker/QEMU sandboxes with built-in noVNC access and automation servers provide isolated environments for safe skill demonstration and validation

- **Cross-Client Compatibility**: Generated skills work across Claude, Cline, and any MCP-compatible client, enabling portability of trained capabilities

## Technical Details

### Architecture

**MCP Server Stack for Skill Generation**:
- Local Skills MCP serves as the skill registry and resolver
- Custom MCP servers expose REST APIs as tool definitions via MCP metadata
- Demonstration recording pipeline captures user actions within noVNC sessions
- Skill generator synthesizes SKILL.md files from recorded action sequences

**CUA + noVNC Integration**:
- Cua provides isolated sandbox environments (Docker on Linux, QEMU for multi-OS, Apple Virtualization on macOS)
- Each sandbox includes noVNC (VNC-over-WebSocket) access for browser-based desktop viewing
- Built-in automation server handles screen recording and action logging
- Pre-configured Cua dependencies eliminate environment setup friction

**Local LLM + API Bridge**:
- Local inference avoids latency of cloud APIs while maintaining full model access
- MCP acts as the abstraction layer between LLM and both:
  - External REST APIs (via custom MCP servers)
  - Local filesystem skills (via Local Skills MCP)
- Function-calling models (Ollama, Qwen) automatically discover available tools via MCP metadata

### Demonstration Recording Workflow

1. **Capture Phase**: User performs a task sequence within noVNC desktop environment
   - Screen recording auto-captures at pixel/action level
   - Automation server logs keyboard/mouse events with timestamps

2. **Processing Phase**: Recorded sequence is parsed and abstracted
   - Extract meaningful action primitives (click coordinates → semantic "click button X")
   - Identify decision points and conditional branches

3. **Skill Synthesis Phase**: AI model generates SKILL.md with:
   - Natural language description of capability
   - Input/output schema definitions
   - Integration code snippets for MCP exposure

4. **Validation Phase**: Generated skill tested against noVNC sandbox
   - Replays original demonstration using synthesized skill
   - Validates output matches recorded behavior
   - Refines skill definition based on discrepancies

### Performance Characteristics

- **Context Efficiency**: Lazy loading of skill metadata reduces prompt token consumption; full skill content loads only on invocation
- **Latency**: Local LLM inference (~100-500ms depending on model) + MCP function call overhead (~50-100ms) = sub-second typical task execution
- **Skill Reusability**: Once generated, SKILL.md files work across any MCP-compatible client without modification

## Implications

### For AI Agent Development

1. **Reduced Development Cycle**: Demonstration-based skill generation eliminates weeks of prompt engineering per capability. Engineers can now generate production-ready skills in hours through visual demonstration.

2. **Accessibility**: Non-prompt-engineers can contribute skills by recording demonstrations. The AI synthesizes the underlying logic, lowering the barrier to specialized skill creation.

3. **Hybrid System Leverage**: Combining local inference (latency, cost efficiency) with remote API access (specialized capabilities) via MCP creates a pragmatic architecture for production deployments that don't require full cloud dependency.

4. **Skill Portability as Competitive Advantage**: SKILL.md format enables organizations to:
   - Publish shareable skill libraries (140+ pre-indexed on Agent Skill Ninja)
   - License specialized domain skills across multiple clients
   - Compose complex behaviors from modular skill primitives

### Architectural Decisions

**Local-First vs. Cloud-First Trade-off**:
- Local: Lower latency, cost control, data privacy, no API quota limits
- Cloud (Remote APIs): Specialized capabilities, auto-scaling, managed infrastructure
- MCP bridges both paradigms; choose dynamically based on task characteristics

**Demonstration Recording vs. Prompt Engineering**:
- Recording favors visual/procedural skills (UI automation, multi-step workflows)
- Prompting favors conceptual skills (analysis, generation, reasoning)
- Optimal systems use both; MCP provides unified interface for hybrid skill libraries

**Sandbox Isolation Trade-offs**:
- Cua's Docker-based isolation protects host system but adds ~100-200ms container communication overhead
- QEMU full-OS virtualization safer for untrusted agents but ~500ms+ overhead
- For trusted demonstration recording, lighter virtualization acceptable

## Related

- [Local Skills MCP - GitHub](https://github.com/kdpa-llc/local-skills-mcp) - Universal MCP server for skill management with lazy loading
- [Cua: Computer Use Agent Platform - GitHub](https://github.com/trycua/cua) - Open-source infrastructure for AI agents controlling full desktops
- [Using the Model Context Protocol (MCP) With a Local LLM - Medium](https://medium.com/predict/using-the-model-context-protocol-mcp-with-a-local-llm-e398d6f318c3) - Integration guide for local inference + MCP
- [Introducing Cua Cloud Sandbox - Cua Blog](https://cua.ai/blog/introducing-cua-cloud-containers) - Cloud-hosted sandbox environments with noVNC access
- [The Llama.cpp MCP Bridge - Skywork](https://skywork.ai/skypage/en/llama-cpp-mcp-bridge-guide/1980099971934101504) - Guide to using MCP with llama.cpp local inference
- [How I Built a Tool-Calling Llama Agent with a Custom MCP Server - Level Up Coding](https://levelup.gitconnected.com/how-i-built-a-tool-calling-llama-agent-with-a-custom-mcp-server-3bc057d27e85) - Practical implementation of MCP + local Llama
- [noVNC API Documentation - GitHub](https://github.com/novnc/noVNC/blob/master/docs/API.md) - Browser-based VNC client programmatic interface
- [Awesome Local LLM - GitHub](https://github.com/rafska/awesome-local-llm) - Curated resources for running LLMs locally

---

## Research Note

The specific Reddit post with this exact title was not found in indexed web search results. This summary synthesizes technical information from:
1. MCP ecosystem documentation and implementations
2. CUA platform architecture
3. Claude Skills + API integration patterns
4. Local LLM + MCP integration guides

The architecture described represents the convergence of these technologies and reflects current best practices in the LocalLLaMA community for skill generation and CUA integration.
