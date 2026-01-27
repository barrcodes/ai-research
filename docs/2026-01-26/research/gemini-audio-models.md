---
title: Improved Gemini audio models for powerful voice experiences
source: Google Blog
url: https://blog.google/products/gemini/gemini-audio-model-updates/
researched_at: 2026-01-26T00:00:00Z
---

# Improved Gemini Audio Models for Powerful Voice Experiences

## Overview

Google released Gemini 2.5 Flash Native Audio with significant improvements in function calling reliability (71.5% accuracy on ComplexFuncBench Audio), instruction adherence (84% → 90%), and multi-turn conversation quality. The update eliminates speech-to-text conversion layers for direct audio understanding, enabling conversational AI with genuine tone and emotional nuance. Paired with enhanced text-to-speech models, these improvements shift voice applications from utility-focused to genuinely natural interactions.

## Key Points

- **Native audio processing**: Direct audio→audio flow preserves prosody, emotion, and conversational context without intermediate text conversion; critical architectural shift for latency-sensitive applications

- **Function calling reliability**: 71.5% ComplexFuncBench Audio score represents measurable improvement in triggering external APIs mid-conversation and integrating results back into speech responses; essential for voice-first CRM and transaction systems

- **Instruction adherence increased to 90%**: Six-percentage-point improvement over prior version ensures consistent enterprise behavior across system prompts, customer service guidelines, and workflow constraints

- **Live speech translation**: Supports 70+ languages with style transfer preserving speaker intonation and emotional tone; operates in continuous-listening or two-way conversation modes

- **Text-to-speech expressivity**: Flash TTS (low-latency) and Pro TTS (high-fidelity) variants with role adherence, context-aware pacing that captures narrative rhythm, and multi-speaker consistency across 24 languages

## Technical Details

| Component | Metric | Notes |
|-----------|--------|-------|
| Function Calling | 71.5% ComplexFuncBench Audio | Enables reliable tool integration during live conversations |
| Instruction Adherence | 90% | Up from 84%; critical for enterprise compliance |
| TTS Models | Flash + Pro variants | Latency vs. quality trade-off; both support 24 languages |
| Speech Translation | 70+ languages | Native style transfer; preserves intonation |
| API Release | gemini-2.5-flash-native-audio-preview-12-2025 | December 12, 2025 |

**Architecture implication**: Moving from transcription-based (speech→text→audio) to native audio endpoints reduces pipeline complexity and latency, especially for stateful conversations where context accumulation across turns matters.

**Implementation available** through Gemini API, Google AI Studio, and Vertex AI Live API with support for asynchronous function calls.

## Implications

**For product teams**: Voice applications can now legitimately compete with text interfaces on conversational quality. Shopify reported users forget they're talking to AI within minutes. This makes voice-first UX viable for customer service, sales, and transaction workflows rather than edge-case accessibility features.

**For platform architects**: Native audio removes the transcription bottleneck that historically introduced latency and lost emotional/contextual information. Trade-offs are clear: Flash TTS prioritizes speed (real-time voice agents), Pro TTS prioritizes quality (production audio content). Function calling reliability at 71.5% suggests edge cases remain—complex multi-step sequences may still need fallbacks.

**For compliance/enterprise**: 90% instruction adherence is materially better but not perfect. High-stakes applications (financial, healthcare) should test against their specific prompt constraints before production. Style transfer in translation preserves brand voice and speaker intent—valuable for global customer service but requires validation that translations maintain compliance intent.

**Adoption friction**: Requires rethinking conversation architecture (stateful multi-turn rather than per-request). Teams deeply invested in transcription pipelines face integration work.

## Related

- [Gemini API Changelog](https://ai.google.dev/gemini-api/docs/changelog) - Complete release notes with breaking changes (e.g., `total_reasoning_tokens` → `total_thought_tokens`)
- [Google Search Live with Gemini 2.5 Flash Native Audio](https://9to5google.com/2025/12/12/google-search-live-native-audio/) - Real-world deployment context
- [How to use Gemini Live API Native Audio in Vertex AI](https://cloud.google.com/blog/topics/developers-practitioners/how-to-use-gemini-live-api-native-audio-in-vertex-ai) - Implementation guide
