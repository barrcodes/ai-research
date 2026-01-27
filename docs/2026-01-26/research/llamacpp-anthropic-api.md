---
title: New in llama.cpp: Anthropic Messages API
source: Hugging Face Blog (GGML-org)
url: https://huggingface.co/blog/ggml-org/anthropic-messages-api-in-llamacpp
researched_at: 2026-01-26T00:00:00Z
---

# New in llama.cpp: Anthropic Messages API

## Overview

llama.cpp now implements the Anthropic Messages API specification, enabling drop-in Claude compatibility for locally-running inference servers. This eliminates the need for external API calls while maintaining full protocol compatibility, unlocking local agentic workloads with tool use, vision, and extended thinking.

## Key Points

- **API Parity**: Full `/v1/messages` endpoint with streaming, tool use, vision, and token counting
- **Transparent Translation**: Architecture converts Anthropic format to OpenAI internally, reusing the mature llama.cpp inference pipeline
- **Zero External Calls**: All processing stays local—no fallback to Anthropic's API, true privacy compliance for enterprise deployments
- **Broad Model Support**: Works with any GGUF-quantized model; recommended for agentic tasks: Nemotron, Qwen3 Coder, Kimi K2, MiniMax M2

## Technical Details

**Architecture Pattern:**
The implementation maintains protocol compatibility by translating Anthropic requests through to llama.cpp's existing OpenAI-compatible internals. This design minimizes new code paths and leverages years of optimization in the core inference engine.

**Supported Features:**
| Feature | Status | Notes |
|---------|--------|-------|
| Chat completions | Full | Streaming + non-streaming |
| Token counting | Full | Pre-generation cost estimation |
| Tool use | Full | `tool_use` and `tool_result` blocks |
| Vision | Full | Base64/URL images with multimodal models |
| Extended thinking | Full | Reasoning token routing |
| Server-sent events | Full | Proper Anthropic event types |

**Integration with Claude Code:**
```bash
llama-server -hf unsloth/Qwen3-Next-80B-A3B-Instruct-GGUF:Q4_K_M
ANTHROPIC_BASE_URL=http://127.0.0.1:8080 claude
```
Requires `ANTHROPIC_AUTH_TOKEN` and `ANTHROPIC_MODEL` environment variables.

## Implications

**For Architecture Decisions:**
- **Cost elimination**: No per-token charges for development, testing, or inference-heavy workloads
- **Latency control**: Local inference removes network round-trip overhead (critical for agentic loops with many tool calls)
- **Model flexibility**: Swap quantization levels (Q4, Q5, Q6) per deployment without changing application code
- **Trade-off**: Requires hardware capable of running 7B–80B+ models; offloads infrastructure investment from API provider to your hardware

**When to Use Locally:**
- Agentic systems with frequent tool invocations (where latency compounds)
- Sensitive data workflows (healthcare, financial records)
- Inference cost optimization at scale (>10M tokens/month equivalent)
- Multi-region or air-gapped deployments

**Alternatives to Consider:**
- Anthropic's hosted API for rapid prototyping and predictable scaling
- Hybrid: local models for high-volume standard tasks, API for reasoning-heavy work
- Other local inference servers (text-generation-webui, vLLM) lack Anthropic protocol support

## Related

- [llama.cpp GitHub](https://github.com/ggml-org/llama.cpp) - Full source and PR #17570 (implementation)
- [Anthropic Messages API docs](https://docs.anthropic.com/en/api/messages) - Authoritative protocol specification
- [Claude Code](https://github.com/anthropics/claude-code) - Local integration via `ANTHROPIC_BASE_URL`
