# EchoEntry - Fine-tuned Whisper for Digit-Specific Transcription

| | |
|---|---|
| **Source** | Reddit r/MachineLearning |
| **URL** | [old.reddit.com/r/MachineLearning/comments/1qwoo8o](https://old.reddit.com/r/MachineLearning/comments/1qwoo8o/p_finetuned_whispersmall_for_digitspecific/) |
| **Researched** | 2026-02-06 |

## Overview

EchoEntry is a specialized deployment of Whisper-small fine-tuned exclusively for numeric transcription tasks. The project demonstrates how domain-specific fine-tuning on relatively modest hardware can achieve 95-99% accuracy on digit sequences while maintaining CPU-only inference below 1 second latency. This represents a pragmatic solution to a well-known weakness in generic ASR models: distinguishing between similar-sounding numbers like "105" vs "15".

## Key Points

- **Model specialization**: Base Whisper-small (1.7GB) fine-tuned on numeric-only dataset rather than general-purpose speech
- **Training approach**: Synthetic TTS data plus native voice recordings covering 1-999 across 5 English accents (US, UK, Irish, Australian, Canadian)
- **Constrained output space**: Forced numeric transcription with digit extraction - eliminates vocabulary bloat from general ASR
- **Deployment efficiency**: FastAPI service runs on 8GB CPU with sub-second inference; no GPU required
- **Accuracy metrics**: 95-99% on 1-3 digit sequences, with multi-accent robustness
- **API-first design**: RESTful endpoint with API key authentication for audio submission

## Technical Details

### Architecture Decisions
- **Greedy decoding** (num_beams=1) prioritizes speed over beam search sophistication - reasonable for constrained numeric output
- **Forced decoder IDs** for English language task prevents model drift to other languages
- **Audio preprocessing**: Librosa/FFmpeg with silence trimming (top_db=35) optimizes for noisy environments

### Training Dataset Construction
- Combined TTS-generated audio (scalable, consistent) with native recordings (natural variation)
- Five accent variants crucial for robustness - critical for digit accuracy where vowel pronunciation critically affects "five" vs "nine" distinction
- Range 1-999 covers standard use cases (phone numbers, account IDs, codes)

### Inference Optimization
- CPU-only inference eliminates deployment infrastructure overhead
- Sub-second latency achievable on modest hardware
- Trim silence preprocessing reduces effective audio duration for transcription

### Noted Challenges
**Browser audio quality gap**: Significant accuracy drop between browser-captured audio and native recordings - indicates audio codec/quality sensitivity. This drove the pivot away from web UI to API model where developers control audio capture pipeline.

## Implications

### For Speech Recognition Practitioners
1. **Narrow task specialization pays**: Forcing models to numeric-only output dramatically improves accuracy vs. general-purpose baselines - relevant for IVR systems, voice authentication, phone banking
2. **Audio capture pipeline matters**: Model development in isolation misleading; production accuracy depends heavily on client-side audio preprocessing and encoding
3. **Synthetic data viability**: TTS + native hybrid approach balances scalability with naturalism for domain-specific tasks
4. **Accent coverage is architectural requirement**: Not an afterthought - multi-accent training is essential for numeric robustness

### For Production Deployment
1. **CPU sufficiency**: Fine-tuning enables practical deployment without GPU infrastructure - cost and latency implications for edge/embedded use
2. **API boundary choice**: Moving from client-side inference (browser) to API prevents audio quality variability - forces audio handling consistency
3. **Forced decoding patterns**: Constraining output space (numeric digits only) is a practical technique for domain-specific models - reduces beam search overhead, prevents hallucination

### For Model Development Strategy
1. **Task decomposition wins**: Treating numeric transcription as separate from general ASR rather than as downstream post-processing
2. **Synthetic data sufficiency**: Demonstrates TTS + small native dataset can achieve 95%+ accuracy - relevant for other domain-specific speech tasks
3. **Inference-first optimization**: Sub-second CPU inference shapes architecture decisions (greedy decoding, preprocessing) rather than accuracy-first approach

## Technical Trade-offs

| Aspect | Decision | Rationale |
|--------|----------|-----------|
| Model | Whisper-small vs large | 1.7GB footprint enables broader deployment, sufficient for constrained numeric task |
| Training data | TTS + native hybrid | Scalability of synthetic balanced against naturalism of real audio |
| Decoding | Greedy vs beam search | Speed prioritized for constrained output space where greedy performs adequately |
| Deployment | API vs browser inference | Eliminates audio quality variability from client-side encoding |
| Output constraint | Forced digit decoder IDs | Prevents model from generating non-numeric tokens, improves accuracy and latency |

## Sources

- [EchoEntry API Documentation](https://echoentry.ai/docs.html) - Official service documentation
- [EchoEntry Test Audio](https://echoentry.ai/test_audio.wav) - Example audio for testing
- OpenAI Whisper model family - Foundation model for fine-tuning approach
