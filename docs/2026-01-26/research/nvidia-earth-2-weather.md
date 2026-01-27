---
title: NVIDIA Launches Earth-2 Family of Open Models — the World's First Fully Open, Accelerated Set of Models and Tools for AI Weather
source: NVIDIA Blog
url: https://blogs.nvidia.com/blog/nvidia-earth-2-open-models/?ncid=so-twit-259380
researched_at: 2026-01-26T00:00:00Z
---

# NVIDIA Earth-2 Weather Models

## Overview

NVIDIA released Earth-2, a comprehensive suite of open-source AI models for weather forecasting with three specialized architectures: Atlas (medium-range forecasting), StormScope (nowcasting), and HealDA (data assimilation). The models deliver 60-500x speedups over traditional methods while achieving parity or better performance on standard meteorological benchmarks, enabling real-time hyperlocal weather predictions at GPU speeds.

## Key Points

- **Atlas Architecture**: Forecasts up to 15 days across 70+ weather variables with superior performance vs. open-source baselines on industry benchmarks
- **StormScope Nowcasting**: Generative AI framework producing kilometer-resolution 6-hour forecasts in minutes; first AI system outperforming physics-based models for precipitation prediction
- **HealDA Data Assimilation**: Generates atmospheric initial conditions in seconds on GPUs instead of hours on supercomputers by synthesizing diverse observation sources
- **CorrDiff Downscaling**: Continental-to-regional resolution enhancement 500x faster than traditional dynamical downscaling
- **Fully Open Stack**: Models on Hugging Face and GitHub; integration framework (Earth2Studio) supports customization via PhysicsNeMo
- **Established Adoption**: TotalEnergies, Eni, Southwest Power Pool, and major meteorological services already deploying for energy forecasting and grid operations

## Technical Details

| Component | Architecture | Key Metric | Use Case |
|-----------|--------------|-----------|----------|
| Medium Range | Atlas | 15-day forecasts, 70+ variables | Grid operations, long-term planning |
| Nowcasting | StormScope | Kilometer resolution, 6-hour windows | Severe weather, hyperlocal prediction |
| Assimilation | HealDA | Seconds vs. hours initialization | Observation integration at scale |
| Downscaling | CorrDiff | 500x speedup | Regional detail from global models |
| Inference | FourCastNet3 | 60x faster than ensemble methods | Operational throughput |

The framework integrates contributions from ECMWF, Microsoft, and Google, targeting GPU acceleration across inference and training. Israel Meteorological Service achieved 90% compute reduction at 2.5km resolution—demonstrating operational viability.

## Implications

**For practitioners**: This dissolves barriers around weather model inference latency. Previously, high-resolution regional forecasts required expensive supercomputer access; now they run on standard GPU clusters in minutes. Organizations can now iterate on custom models (e.g., for specific energy assets or geographic regions) via PhysicsNeMo without building from scratch.

**Key trade-offs**: Atlas trades short-term (sub-12hr) accuracy for medium-range capability; StormScope targets nowcasting specifically. Practitioners need to select the right model for their use case rather than expecting a unified solution.

**Competitive shift**: Open-source + GPU-native removes incumbent vendors' moat on forecast compute. Organizations historically locked into proprietary weather service subscriptions can now own their prediction stack, though this assumes internal ML/MLOps capability.

## Related

- [Earth2Studio GitHub](https://github.com/NVIDIA/earth2studio) - Framework for building custom weather models
- [NVIDIA PhysicsNeMo](https://docs.nvidia.com/nemo-framework/) - Physics-informed neural network customization
- [Hugging Face Earth-2 Models](https://huggingface.co/models?search=earth2) - Model hub with pre-trained weights
