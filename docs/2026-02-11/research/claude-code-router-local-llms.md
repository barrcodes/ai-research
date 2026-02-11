# Claude Code Router with Local LLMs

| | |
|---|---|
| **Source** | r/LocalLLaMA |
| **URL** | [old.reddit.com/r/LocalLLaMA/comments/1r1xqjp/](https://old.reddit.com/r/LocalLLaMA/comments/1r1xqjp/claude_code_router_with_local_llms/) |
| **Researched** | 2026-02-11 |

## Overview

Claude Code architecture uses tool-calling as its primary integration mechanism for file I/O, execution, and system interaction. When routing Claude Code requests to local LLMs via llama.cpp, the critical integration point is **proper tool definition passing and function calling format compliance**, not model capability alone. Integration failures typically stem from OpenAI-compatible endpoints not properly forwarding tool definitions or models generating malformed tool responses rather than architectural mismatches.

## Key Points

- **Tool-based architecture**: Claude Code executes file operations (Read, Write, Bash) via tool calls rather than direct model access—the model generates structured requests that Claude Code's runtime interprets and executes locally
- **Function calling format critical**: Local models must output properly formatted tool_calls (OpenAI-compatible format) with correct field names; text-only fallback responses indicate tool calling is not working, not that the model can't conceptually handle files
- **Endpoint-model-build alignment**: Three failure vectors must align: model must support function calling (version/variant dependent), llama.cpp endpoint must pass tool definitions via API, and llama.cpp build/chat template must be current and model-specific
- **Model version specificity**: Qwen 2.5 lacks tool support; Qwen 3+ required. Gemma 27B and Qwen Coder support function calling but need proper configuration. Device speed matters—large models slow enough to degrade interactive experience
- **Alternative agents available**: Opencode and Crush agents mentioned as pragmatic alternatives that work better with local llama.cpp deployments; Claude Code's large system prompt creates performance/latency challenges at smaller model sizes

## Technical Details

### Integration Pattern

When Claude Code invokes a local LLM endpoint:

1. System prompt includes tool definitions (Read, Write, Bash, etc. with JSON schemas)
2. Model should respond with `tool_calls` array in OpenAI format:
```
{
  "tool_calls": [
    {
      "id": "call_123",
      "type": "function",
      "function": {
        "name": "read",
        "arguments": "{\"path\": \"/home/user/file.txt\"}"
      }
    }
  ]
}
```
3. Claude Code runtime executes tool and passes result back to model for continued reasoning

### Critical Configuration Points

**llama.cpp Build & Endpoint:**
- Version must be recent enough to handle function calling properly
- OpenAI-compatible endpoint (default on port 8000) must pass `tools` parameter in requests
- Chat template must match model (often requires `--jinja` flag for Qwen/other variants)

**Model Selection (Tested Context):**
- **Qwen 3 / Qwen 3 Next**: Function calling supported, viable
- **Qwen 2.5**: No function calling support, not suitable
- **Gemma 27B**: Function calling supported, but may need template tuning
- **Devstral**: Mentioned but slower on consumer hardware (RTX 3090 Ti)
- **Qwen Coder**: Function calling capable

**Performance Implications:**
- User had RTX 3090 Ti on desktop, MacBook running Claude Code client
- Larger models (27B+) at Q4 quantization slow enough to degrade interactivity
- System prompt of Claude Code (agent reasoning overhead) exacerbates latency with local models

### Discovery Method

Test tool calling without full agent:
1. Use WebUI (e.g., Open WebUI with llama.cpp backend)
2. Pass function calling test prompt with tool definitions
3. Verify model responds with `tool_calls` array in response, not text like "I cannot access files"
4. If only text output, model/endpoint not configured for function calling

## Implications

### For Architecture Decisions

**Local LLM + Claude Code integration is viable but requires proper tool infrastructure:**
- Not a model capability problem; it's an integration configuration problem
- Tool definition passing must work end-to-end: client → endpoint → model → response parsing
- Qwen 3+ and models with explicit function calling training are required; Qwen 2.5 era models are incompatible

**Performance trade-off at scale:**
- Consumer-grade inference (single RTX 3090 Ti) limits effective model size
- Claude Code's large system prompt makes latency visible on local inference
- Alternative agents (Opencode, Crush) may be better pragmatic choice for local-only workflows; they likely have smaller system prompts and lower token overhead

### For Integration Practice

1. **Version lock critical models**: Not all Qwen versions support tools; explicit documentation required
2. **Test tool format separately**: Don't debug agent behavior first; isolate tool calling on simple prompts
3. **Understand your endpoint**: OpenAI-compatible doesn't guarantee feature parity; verify tools parameter support
4. **Consider inference speed**: Local models benefit from smaller system prompts and fewer intermediate reasoning steps

### Trade-off Analysis

**Claude Code + Local LLM:**
- Pros: Full privacy, local execution, file I/O via tool calls
- Cons: Latency with large system prompts, requires proper function calling setup, consumer hardware bottleneck
- Best for: Privacy-first workflows where latency acceptable, or beefy local hardware (multi-GPU)

**Alternative agents (Opencode, Crush):**
- Likely pros: Smaller system prompts, better local LLM performance
- Cons: Different reasoning style, may lack some Claude Code features
- Best for: Fast iteration with local inference on limited hardware

## Sources

- [Claude code router with local LLMs? - r/LocalLLaMA](https://old.reddit.com/r/LocalLLaMA/comments/1r1xqjp/claude_code_router_with_local_llms/) - Original question and community answers on tool integration patterns, model requirements, and alternative agents
- Comment by RobertLigthart: Tool calling architecture, model format requirements, llama.cpp configuration issues
- Comment by RadiantHueOfBeige: Model version specificity (Qwen 2.5 vs 3), chat template requirements, alternative agent suggestions (Opencode, Crush)
- Comment by HarjjotSinghh: Pragmatic warning on performance/latency considerations with local inference (sarcastic tone but flags real productivity risk)
