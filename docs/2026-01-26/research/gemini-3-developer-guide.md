---
title: Start building with Gemini 3
source: Google Developers Blog
url: https://blog.google/technology/developers/gemini-3-developers/
researched_at: 2026-01-26T00:00:00Z
---

# Start Building with Gemini 3

## Overview

Google released Gemini 3 with new agentic reasoning capabilities and granular control over reasoning depth, latency, and cost. The model family introduces `thinking_level` and `media_resolution` parameters that let developers optimize for their specific use cases—from reasoning-intensive analysis to high-throughput structured extraction.

## Key Points

- **Three models in preview**: `gemini-3-pro-preview` (broad reasoning), `gemini-3-flash-preview` (pro-level speed), and `gemini-3-pro-image-preview` (image generation)
- **Thinking level control**: Set `thinking_level` to "high" for complex analysis or "low" for latency-sensitive workloads—with "minimal" and "medium" options on Flash
- **Media resolution granularity**: Four settings (`low`/`medium`/`high`/`ultra_high`) to balance visual fidelity against token consumption
- **Thought signatures enforced**: Encrypted reasoning representations must persist across multi-turn conversations—critical for agentic workflows and automatically handled by official SDKs
- **1M token context window**: Supports complex document processing with January 2025 knowledge cutoff

## Technical Details

### Pricing & Cost Control

| Model | Input | Output | Notes |
|-------|-------|--------|-------|
| Gemini 3 Pro | $2/1M tokens | $12/1M tokens | $4/$18 for 200k+ token requests |
| Gemini 3 Flash | $0.50/1M tokens | $3/1M tokens | Pro-level quality at Flash speed |

Google Search grounding moved from flat $35/1k-prompt pricing to usage-based $14/1k-search-queries—significant for high-volume agent applications.

### Getting Started

```python
from google import genai

client = genai.Client()
response = client.models.generate_content(
    model="gemini-3-pro-preview",
    contents="Your prompt here"
)
```

**Media resolution guidance by media type**:
- Images: `high` (1120 tokens)
- PDFs: `medium` (560 tokens)
- Video: `low`/`medium` (70 tokens per frame)

### Thought Signature Handling

**Critical requirement**: Thought signatures (encrypted representations of internal reasoning) are mandatory even at `thinking_level=minimal`. Official SDKs handle this transparently in chat workflows, but function calling and image generation require manual management—preserve and pass back in subsequent requests to maintain reasoning chain.

**Warning**: Do not combine `thinking_level` with legacy `thinking_budget` parameter.

### Supported Tools

- Google Search (grounding)
- File Search
- Code Execution
- URL Context
- Function Calling
- Structured Outputs (new with grounding)

*Not yet supported*: Maps Grounding, Computer Use.

## Implications

**For practitioners**: Gemini 3 presents actionable trade-offs. The `thinking_level` parameter lets you optimize per-request rather than picking one model. For high-volume extraction, set `low` thinking + appropriate `media_resolution` and watch token costs. For one-off strategic analysis, max out reasoning depth.

**Architecture consideration**: Thought signatures require state management in agentic systems. If you're building multi-step agents, ensure your conversation loop captures and returns thought signatures—not optional for reasoning consistency.

**Cost optimization**: Media resolution is your primary lever for vision workloads. Test `medium` on PDFs before upgrading; video at `low` eliminates per-frame token bloat.

**API management**: Keep temperature at 1.0 (default) for reasoning tasks. Lowering causes looping on complex problems. Place specific constraints at prompt start or in system instructions for consistency with large contexts.

## Related

- [Gemini API Documentation](https://ai.google.dev/gemini-api/docs/gemini-3) - Official technical reference
- [Getting Started with Gemini 3 Flash](https://cloud.google.com/blog/topics/developers-practitioners/getting-started-with-gemini-3-hello-world-with-gemini-3-flash) - Quick start guide
- [Gemini Code Assist with Gemini 3](https://developers.google.com/gemini-code-assist/docs/gemini-3) - IDE integration
- [Partner & Library Integrations](https://ai.google.dev/gemini-api/docs/partner-integration) - Third-party SDK support

---

**Sources**:
- [Gemini 3 Developer Guide | Google AI for Developers](https://ai.google.dev/gemini-api/docs/gemini-3)
- [New Gemini API updates for Gemini 3 - Google Developers Blog](https://developers.googleblog.com/new-gemini-api-updates-for-gemini-3/)
- [Getting Started with Gemini 3 Flash](https://cloud.google.com/blog/topics/developers-practitioners/getting-started-with-gemini-3-hello-world-with-gemini-3-flash)
