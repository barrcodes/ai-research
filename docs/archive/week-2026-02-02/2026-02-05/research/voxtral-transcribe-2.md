# Voxtral Transcribe 2

| | |
|---|---|
| **Source** | Mistral AI |
| **URL** | [mistral.ai/news/voxtral-transcribe-2](https://mistral.ai/news/voxtral-transcribe-2) |
| **Researched** | 2026-02-05 |

## Overview

Mistral released two specialized transcription models: Voxtral Mini Transcribe V2 (API) optimized for cost-performance, and Voxtral Realtime (open-source) with streaming architecture delivering sub-200ms latency. Both achieve industry-leading accuracy at ~4% WER while handling 13 languages, speaker diarization, and domain-specific context biasing.

## Key Points

- **Streaming Architecture**: Voxtral Realtime transcribes audio as it arrives rather than batch-processing chunks—enabling real-time voice applications with configurable latency below 200ms
- **Cost-Performance Leader**: Mini Transcribe V2 at $0.003/min outperforms GPT-4o mini, Gemini 2.5 Flash, and Deepgram Nova while processing ~3x faster than ElevenLabs Scribe v2
- **Enterprise Features**: Speaker diarization with precise timing, context biasing (100-word vocabulary injection), word-level timestamps for subtitle alignment, and 3-hour max audio duration
- **Multilingual + Robust**: Supports 13 languages with documented noise robustness for challenging acoustic environments
- **Open-Source Edge Deployment**: Voxtral Realtime released under Apache 2.0 with 4B parameters—enabling on-device privacy-preserving transcription

## Technical Details

**Model Specialization**: The two-model approach separates concerns—Mini for batch/API (optimized for cost and accuracy), Realtime for streaming (optimized for latency and privacy). This architecture mirrors successful multimodel strategies in other domains where different use cases demand different trade-offs.

**Streaming Implementation**: Sub-200ms latency suggests aggressive state management and buffer management—likely leveraging streaming-specific architectural patterns (stateful chunking, incremental output) rather than applying traditional batch processing to windowed streams.

**Diarization Quality**: Superior speaker attribution across benchmarks indicates the models handle overlapping speech and speaker transitions better than competitors—relevant for meeting transcription and multi-speaker content.

| Metric | Voxtral Mini V2 | Value |
|--------|---|---|
| WER (FLEURS) | ~4% | Industry-leading |
| Cost | $0.003/min | Lowest in category |
| Speed vs ElevenLabs | 3x faster | Scribe v2 baseline |
| Max Audio | 3 hours | Single request |
| Languages | 13 | Including CJK, Arabic |

## Implications

**For Real-Time Voice Systems**: Voxtral Realtime's sub-200ms latency and open-source Apache 2.0 license enable building voice-first applications (voice AI assistants, accessibility tools) without API dependency or latency concerns. The 4B parameter footprint fits edge devices—critical for privacy-sensitive use cases where audio shouldn't leave infrastructure.

**For Cost-Conscious Batch Processing**: Mini Transcribe's $0.003/min pricing and 3x speed advantage makes it the default choice for high-volume transcription workloads. The 4% WER meets accuracy requirements for most applications except medical/legal where domain-specific context biasing (100 words) provides flexibility.

**Architectural Decision Point**: Choose Realtime (open-source) for latency-critical or privacy-required paths; choose Mini API for cost-optimized, throughput-heavy workloads. The context biasing feature suggests both models incorporate vocabulary injection—watch for latency/accuracy trade-offs when using this for specialized domains.

**Competitive Landscape**: Mistral is directly challenging OpenAI and Google's transcription offerings with better pricing and speed. This signals accelerating commoditization of transcription—margins compress, differentiation shifts to latency, accuracy, and niche features (diarization quality, multilingual robustness).

## Sources

- [Mistral AI - Voxtral Transcribe 2](https://mistral.ai/news/voxtral-transcribe-2) - Official announcement with technical specifications
