---
title: Introducing SAM 3D - Powerful 3D Reconstruction for Physical World Images
source: Meta AI Blog
url: https://ai.meta.com/blog/sam-3d/
researched_at: 2026-01-26T00:00:00Z
---

# Introducing SAM 3D: Powerful 3D Reconstruction for Physical World Images

## Overview

Meta released SAM 3D, a foundation model that reconstructs complete 3D geometry and texture from single images. The system extends the Segment Anything Model (SAM) paradigm to 3D space through two specialized models: one for general object/scene reconstruction and one for human body analysis. It achieves 5:1 performance advantage over competing systems in human preference testing.

## Key Points

- **Dual-Model Architecture**: SAM 3D Objects for scene reconstruction; SAM 3D Body for human pose/shape estimation with interactive prompting capability
- **Transformer-Based Design**: Encoder-decoder architecture adapted from vision transformers, enabling both 2D segmentation prompts and 3D output prediction
- **Sim-to-Real Training Pipeline**: Pre-training on ~3.14M synthetic meshes from 1M annotated images, followed by post-training on 8M diverse natural images to close the synthetic-to-real gap
- **Performance Benchmark**: 5:1 win rate vs. leading alternatives in human evaluation; textured 3D output generated in seconds
- **Production Deployment**: Already live in Facebook Marketplace's "View in Room" feature for furniture/home décor visualization

## Technical Details

**Training Approach**: Models follow LLM-style pre-training/post-training paradigm. SAM 3D Objects trained on model-generated geometry; SAM 3D Body leverages multi-camera capture systems and photogrammetry to ground pose estimation in natural image distributions.

**Output Format**: SAM 3D Body uses Meta Momentum Human Rig (MHR), an open-source skeleton+shape decomposition that separates skeletal kinematics from soft tissue deformation—critical for downstream applications like animation or measurement.

**Inference Speed**: Optimized diffusion techniques deliver complete textured 3D reconstructions within seconds, suitable for real-time applications in robotics and interactive systems.

**Known Limitations**: Human-object interaction reconstruction and multi-person scenarios remain open challenges.

## Implications

**For Product Teams**: Single-image 3D reconstruction now has production viability. The "View in Room" pattern validates e-commerce application; expect similar integrations in furniture/fashion/real estate platforms. The 5:1 quality advantage provides defensible competitive positioning.

**For Robotics/Perception**: Real-time 3D reconstruction from monocular input becomes a standard perception module. Eliminates need for stereo/RGBD sensors in many scenarios, reducing hardware complexity.

**For Creative Industries**: Human body reconstruction quality enables new asset generation workflows for gaming and film. MHR format interoperability matters—check downstream tool compatibility.

**Architecture Decision**: Single-image reconstruction trades off completeness for speed/simplicity. Multi-view systems still required for occluded geometry; this complements rather than replaces them.

## Related

- [SAM (Segment Anything Model)](https://github.com/facebookresearch/segment-anything) - 2D foundation model predecessor
- [Meta Momentum Human Rig](https://github.com/facebookresearch/4d-humans) - Open-source skeleton representation
- [3D Gaussian Splatting](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/) - Alternative 3D representation competing for similar applications
