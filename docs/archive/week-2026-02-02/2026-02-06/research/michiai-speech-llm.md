# MichiAI: 530M Full-Duplex Speech LLM with ~75ms Latency

| | |
|---|---|
| **Source** | r/MachineLearning |
| **URL** | [old.reddit.com/r/MachineLearning/comments/1quwjt1/](https://old.reddit.com/r/MachineLearning/comments/1quwjt1/p_michiai_a_530m_fullduplex_speech_llm_with_75ms/) |
| **Researched** | 2026-02-06 |

## Overview

MichiAI demonstrates a pragmatic approach to full-duplex speech synthesis by prioritizing inference latency and training efficiency over model scale. Built on SmolLM 360M backbone with rectified flow matching for continuous audio prediction, the model achieves ~75ms TTFA on a single RTX 4090 while maintaining linguistic coherence through multimodal fusion of text and audio embeddings.

## Key Points

- **Architecture**: 530M parameter full-duplex speech model using rectified flow matching instead of discrete codebooks, enabling single-pass audio prediction vs. 32+ passes required by codec-based approaches
- **Real-time Performance**: ~75ms time-to-first-audio on unoptimized Python inference on RTX 4090; full duplex capable with latency suitable for interactive applications
- **Training Data**: 5,000 hours of audio (LibriVox dataset) with significant quality variability; augmented with pure text samples to preserve language model capabilities
- **Hardware Efficiency**: Primary training on single RTX 4090 with some memory-intensive components on dual A6000 GPUs
- **Coherence Preservation**: Multimodal listen head combining audio embeddings and text tokens; text token integration critical for maintaining coherence during speech generation

## Technical Details

**Core Innovation**: Rectified flow matching for continuous audio embedding prediction eliminates codebook quantization, achieving coherence and latency simultaneously. The model "recycles" pretrained text knowledge from SmolLM without degradation—no visible LM loss curve regression observed during testing.

**Training Architecture**:
- Separable pretraining of components with end-to-end training as final step
- Audio embeddings optimized for beneficial modality fusion between text and speech
- Pure text sample mixing (dataset composition detail not specified) to reinforce language understanding
- LibriVox dataset composition: ~75% low-quality microphone recordings; model learns this characteristic but can be steered toward high-quality output during inference through prompt conditioning

**Inference Optimization**: Model demonstrated ability to voice clone from high-quality speakers post-training, effectively filtering learned noise patterns by conditioning on clean speaker representations. This suggests the model internally separates signal quality without explicit denoising layers.

**Baseline Performance**: Tested models show no visible coherence degradation typical of smaller speech models. Reasoning capabilities match base backbone SmolLM when tested.

## Implications

This work challenges the conventional wisdom that speech synthesis requires either massive parameter counts or acceptable latency trade-offs. Key architectural insights for practitioners:

1. **Modality Fusion Matters**: Adding text conditioning to speech encoding is non-trivial but essential for preserving language model capabilities at scale < 1B parameters
2. **Flow Matching as Alternative to Codebooks**: Single-pass generation with continuous targets offers latency advantages over discrete approaches without proportional quality loss
3. **Constrained Training Viable**: High-quality speech synthesis achievable with budget hardware (single 4090) through careful architecture design rather than scale; 5k hours is substantially less than comparable models
4. **Dataset Quality Less Critical with Proper Conditioning**: LibriVox's notorious quality issues were overcome through inference-time prompt steering, not architecture-level denoising

**Production Readiness**: Current audio quality described as "pretty decent" with metallic artifacts characteristic of undertrained flow matching models. Convergence likely needed before production deployment. Model weights and code appear to be under development (empty GitHub repository at time of posting).

## Sources

- [ketsuilabs.io/blog/introducing-michi-ai](https://ketsuilabs.io/blog/introducing-michi-ai) - Full technical description and audio samples
- [github.com/KetsuiLabs/MichiAI](https://github.com/KetsuiLabs/MichiAI) - Project repository
- [old.reddit.com/r/MachineLearning/comments/1quwjt1/](https://old.reddit.com/r/MachineLearning/comments/1quwjt1/p_michiai_a_530m_fullduplex_speech_llm_with_75ms/) - Discussion thread with author responses
