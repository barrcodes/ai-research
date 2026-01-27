---
title: NVIDIA Earth-2 Open Models Span the Whole Weather Stack
source: Hugging Face Blog
url: https://huggingface.co/blog/nvidia/earth-2-open-models
researched_at: 2026-01-27T00:00:00Z
---

# NVIDIA Earth-2 Open Models Span the Whole Weather Stack

## Overview

NVIDIA released a complete open-source weather forecasting pipeline spanning data assimilation through medium-range predictions. The architecture replaces traditional physics-based numerical weather prediction with end-to-end generative AI models, delivering orders-of-magnitude speedups while maintaining or exceeding forecast accuracy.

## Key Points

- **HealDA (Data Assimilation)**: Generates optimal initial atmospheric conditions in seconds on GPUs versus hours on supercomputers—eliminating the computational bottleneck that historically dominated weather prediction pipelines
- **Earth-2 Medium Range (Atlas)**: 15-day latent diffusion transformer predicting 70+ atmospheric variables; outperforms GenCast on standard benchmarks through incremental change prediction that preserves critical atmospheric structures
- **Earth-2 Nowcasting (StormScope)**: Kilometer-resolution severe weather predictions for zero to six-hour windows; outperforms physics-based models on precipitation forecasting using geostationary satellite data
- **Complete Pipeline**: Earth2Studio provides production inference framework; Physics Nemo enables fine-tuning on custom datasets for sovereign predictions

## Technical Details

| Component | Architecture | Performance | Deployment |
|-----------|--------------|-------------|-----------|
| Data Assimilation | HealDA | Seconds (GPU) vs hours (supercomputer) | GPU inference |
| Medium Range | Latent diffusion transformer | 15-day horizon, 70+ variables | Earth2Studio |
| Nowcasting | StormScope | ~1km resolution, 0-6 hour | GPU-optimized |

The diffusion-based approach for medium-range forecasting generates incremental atmospheric changes rather than direct predictions, reducing error propagation and preserving physical structures critical for forecast skill.

## Implications

**Architectural significance**: This inverts the traditional weather prediction stack. Data assimilation—historically the computational bottleneck—becomes GPU-native and acceleratable, enabling real-time ensemble forecasting. Organizations can now operate sovereign forecasting systems without dependence on centralized supercomputing infrastructure.

**Practical trade-offs**:
- Gains: GPU scalability, sub-second latency, fine-tunable models, lower operational cost
- Constraints: Requires curating satellite/radar datasets for fine-tuning; ensemble generation overhead not explicitly quantified

**Decision point**: This is production-ready for severe weather and nowcasting applications. Medium-range forecasting matches established benchmarks but requires institutional validation before replacing operational physics-based systems.

## Related

- [Earth2Studio Documentation](https://github.com/NVIDIA/earth2studio) - Production inference framework
- [Physics Nemo](https://github.com/NVIDIA/Nemo) - Model training and fine-tuning
- NVIDIA Hugging Face model hub - Pre-trained weights available for immediate use
