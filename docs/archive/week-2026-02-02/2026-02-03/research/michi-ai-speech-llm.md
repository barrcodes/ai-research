# MichiAI: A 530M Full-Duplex Speech LLM with ~75ms Latency

| | |
|---|---|
| **Source** | KetsuiLabs/Damian Krystkiewicz |
| **URL** | [ketsuilabs.io/blog/introducing-michi-ai](https://ketsuilabs.io/blog/introducing-michi-ai) |
| **Researched** | 2026-02-03 |

## Overview

MichiAI is a 530M-parameter speech language model designed for true full-duplex spoken interaction with ~75ms time-to-first-audio latency, achieved through continuous audio embeddings and rectified flow matching instead of traditional quantized approaches. The model maintains the reasoning capabilities of its text LLM backbone (SmolLM-360m) while introducing speech capability trained on only ~5,000 hours of audio—three orders of magnitude less than competing speech LLMs—by effectively leveraging pretrained text knowledge.

## Key Points

- **Architectural Innovation**: Replaces slow RVQ (Residual Vector Quantization) quantization with continuous embeddings decoded in a single forward pass, eliminating the 32+ sequential codebook predictions required by models like Moshi and Sesame.

- **Multimodal Design**: Encodes user input as both audio embeddings AND text tokens rather than audio-only, reducing parameter count and preventing coherence degradation since text embeddings are more information-dense than audio embeddings alone.

- **Extreme Data Efficiency**: Trained on only ~5,000 hours of audio versus 7M-20M+ hours for Hertz-dev, Moshi, and Qwen-Omni. The model "recycles" pretrained text knowledge, learning pronunciation patterns then mapping them back to existing semantic understanding.

- **Zero-Shot Voice Cloning**: Captures speaker timbre and style from seconds of audio; longer input improves "way of speaking" accuracy.

- **Native Full-Duplex**: Handles interjections and backchanneling implicitly without turn-taking latency; listen and speak heads can operate simultaneously.

- **No Coherence Loss**: Internal perplexity tracking and qualitative testing show no reasoning degradation compared to text-only baseline (formal MMLU/HellaSwag evals pending).

## Technical Details

### Architecture

The model uses three core components:

1. **Listening Head**: Multi-modal encoder producing continuous embeddings while simultaneously generating text tokens from raw audio
2. **LLM Backbone**: SmolLM-360m for semantic reasoning
3. **Speaking Head**: Predicts continuous audio embeddings via rectified flow matching, decoded to waveform through lightweight causal HiFi-GAN vocoder

### Why Continuous Embeddings Matter

Traditional RVQ-based speech models (Moshi, Sesame) require predicting 32 codebook entries per 80ms frame—potentially 400+ forward passes to generate a single spoken word. Continuous embeddings allow prediction of the complete acoustic representation in one pass, eliminating quantization artifacts and enabling sub-100ms latency on consumer hardware (RTX 4090, pure Python inference without optimization).

### Training Approach

- **Dataset**: LibriTTS (200h) + LibriVox (4.8k hours) + text corpus (SmolLM-corpus, Cosmopedia-v2)
- **Text Mixing**: Pure text samples mixed into training to preserve text comprehension and support mixed prompting (e.g., "Let me think... [CoT text] ...The answer is...")
- **Hardware**: 1x RTX 4090 for most training; 2x RTX A6000 for memory-intensive components; not trained to convergence
- **Whisper Fine-tuning**: Used hand-transcribed data to ensure precise label-speech alignment

### Demonstrated Capabilities

- Correct homograph pronunciation based on context ("lives" in "nine lives" vs "she lives in NY")
- Automatic number/abbreviation/unit pronunciation without text normalization
- Factually grounded responses from RAG-injected text knowledge
- Forced TTS mode followed by autonomous text+audio generation
- Speaker style matching from audio prompts

## Implications

**For Production Speech Systems**: The continuous embedding approach + modest data requirements dramatically reduce the barrier to entry for building on-device speech LLMs. A 530M model on consumer GPU with 5k hours achieves what previously required 7B+ models and millions of hours.

**For Modality Fusion Research**: The finding that parallel text+audio encoding prevents coherence degradation (vs. audio-only) challenges the assumption that speech models inherently degrade LLM performance. This has architectural implications for future multimodal training.

**For Latency-Critical Applications**: 75ms TTFA on a single GPU enables real-time voice interaction without the serial bottleneck of ASR→LLM→TTS pipelines. This is below the ~500ms threshold identified as breaking conversational naturalness.

**For Data-Constrained Scenarios**: Validation that a speech LLM can train effectively on 5k hours (vs. millions) by reusing text pretraining suggests fine-tuning for domain-specific voice cloning could work with <1 hour speaker data for assistant-like tasks.

**For Architecture Design**: The elimination of quantization in favor of continuous latents is technically debt reduction—floating-point precision prevents RVQ bottlenecks without sacrificing model quality. This pattern may generalize beyond speech.

## Sources

- [ketsuilabs.io/blog/introducing-michi-ai](https://ketsuilabs.io/blog/introducing-michi-ai) - Comprehensive technical blog post with architecture details, performance comparisons, and audio samples
- [github.com/KetsuiLabs/MichiAI](https://github.com/KetsuiLabs/MichiAI) - Official repository with code and documentation
- [Hacker News discussion](https://news.ycombinator.com/item?id=46872573) - Community discussion of the announcement
