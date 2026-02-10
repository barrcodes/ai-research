# Use ANY TTS Engine with ANY AI Chat System

| | |
|---|---|
| **Source** | GitHub (bns25/any-tts) |
| **URL** | [github.com/bns25/any-tts](https://github.com/bns25/any-tts) |
| **Researched** | 2026-02-05 |

## Overview

AnyTTS solves a concrete integration problem: each AI chat system (ChatGPT, Claude, Gemini) requires separate TTS tooling. This project delivers a unified, pluggable TTS abstraction layer that works across any chat interface through an OpenAI-compatible API and clipboard monitoring, supporting seven pre-integrated TTS engines on modest GPU hardware (8GB VRAM).

## Key Points

- **Clipboard-monitored server** intercepts copied text and synthesizes audio without requiring browser extensions or native integrations—clipboard copy workflow triggers synthesis automatically
- **Plugin architecture** (single-file TTS engines) enables extensibility; new engines require only subclassing `TTSEngine` base class without core code modifications
- **OpenAI API compatibility** (`POST /v1/audio/speech` endpoint) allows drop-in integration with existing tools and higher-level frameworks expecting OpenAI's speech interface
- **Seven engines included**: Chatterbox, Coqui XTTS v2, Soprano, Spark TTS, EchoVoice, NeuTTS, VibeVoice—supporting multiple voice profiles and synthesis approaches
- **Streaming chunked audio** reduces latency by delivering synthesized speech in segments rather than waiting for complete generation
- **Gradio UI** provides accessible testing interface without requiring client-side code changes

## Technical Details

**Architecture Pattern:** Server-side facade over heterogeneous TTS engines with three access paths:
1. Clipboard monitor (polling-based automation)
2. REST API (programmatic integration)
3. Web UI (interactive testing)

**Deployment Model:** Isolated virtual environments per engine in `installed-engines/` directory—isolates dependencies and versions without global conflicts.

**Stack:** Python 3.11, CUDA-accelerated GPU synthesis, Windows batch launcher. Runs local LLM integration, enabling completely offline multi-step workflows (LLM → TTS).

**Resource Profile:** All seven engines simultaneously fit within 8GB VRAM constraints, enabling cost-effective deployment on consumer GPUs or shared hardware.

## Implications

**For practitioners:** This eliminates the friction of managing separate TTS solutions per chat platform. The OpenAI API compatibility means you can route speech requests through a single Python process regardless of whether you're using ChatGPT, Claude, Gemini, or local LLMs—critical for building unified agentic workflows where TTS is a non-differentiator.

**Architectural decision:** The clipboard monitor approach trades API coupling (requires users to copy text) for zero-friction UI integration—no browser extension maintenance, no platform-specific bindings. This works anywhere text flows, but introduces latency (copy→detect→synthesize).

**Trade-offs:**
- Streaming support helps, but clipboard monitoring still has ~100-500ms detection overhead vs. native integrations
- Plugin architecture is elegant but testing/validation becomes developer's responsibility
- GPU-centric design limits CPU-only deployments

**When to adopt:** If building LLM-powered applications with speech output requirements, or if you've already invested in a TTS engine and need to expose it across multiple chat interfaces without per-interface wrappers. Less useful if you only need TTS with a single platform.

## Sources

- [bns25/any-tts](https://github.com/bns25/any-tts) - Pluggable TTS abstraction with OpenAI API compatibility
