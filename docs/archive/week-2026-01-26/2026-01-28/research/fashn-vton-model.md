# FASHN VTON v1.5 - Pixel-Space Virtual Try-On Model

| | |
|---|---|
| **Source** | Reddit r/MachineLearning |
| **URL** | [old.reddit.com/r/MachineLearning/comments/1qpc4ap/r_we_opensourced_fashn_vton_v15_a_pixelspace](https://old.reddit.com/r/MachineLearning/comments/1qpc4ap/r_we_opensourced_fashn_vton_v15_a_pixelspace/) |
| **Researched** | 2026-01-28 |

## Overview

FASHN AI open-sourced FASHN VTON v1.5, a production-grade virtual try-on model that generates photorealistic images of people wearing garments. Built from scratch with 972M parameters and trained for under $10k on rented A100s, the model operates directly in pixel space rather than VAE latent space, eliminating artifacts while maintaining efficiency. Apache-2.0 licensed and production-ready on consumer GPUs.

## Key Points

- **Pixel-space architecture**: Operates directly on RGB pixels rather than VAE latent space, preserving fine garment details (textures, patterns, text) that would otherwise blur through VAE encoding/decoding
- **Maskless inference**: No segmentation mask required on target person—the model learns to infer clothing boundaries, improving body preservation and enabling unconstrained garment volume
- **MMDiT foundation**: Multi-Modal Diffusion Transformer with specialized block structure (4 patch-mixer + 8 double-stream + 16 single-stream blocks) for efficient conditioning on person and garment images
- **Rectified Flow sampling**: Uses linear interpolation between noise and data rather than standard diffusion timesteps, improving sampling efficiency
- **Practical deployment**: ~5-second inference on H100, runs on consumer GPUs (RTX 30xx/40xx), requires 8GB VRAM minimum
- **Commercial-friendly**: Apache-2.0 license with full weights and inference code open-sourced, addressing fragmentation of proprietary/restricted VTON models

## Technical Details

### Architecture Stack

The model uses a Multi-Modal Diffusion Transformer (MMDiT) as its core, designed to process three input streams: person image, garment image, and category embedding (tops/bottoms/one-piece). Block composition breaks down as:

- **Patch-mixer blocks (4)**: Encode and fuse modality-specific information early
- **Double-stream blocks (8)**: Maintain separate processing paths for person and garment contexts before fusion
- **Single-stream blocks (16)**: Unified representation learning after cross-modal fusion

The 972M parameter count is competitive for domain-specific diffusion models while remaining deployable. Unlike latent diffusion approaches (e.g., Stable Diffusion variants), this operates in pixel space, which prevents VAE reconstruction artifacts that typically blur fine garment textures and degrade realism.

### Sampling Strategy

The model uses Rectified Flow, a modern sampling paradigm that interpolates linearly between pure noise and the target image distribution. This approach:
- Reduces required sampling steps compared to standard DDPM-style diffusion
- Improves inference latency (critical for real-time applications)
- Maintains visual quality through direct pixel-space supervision

### Training Efficiency

Total training cost of $5-10k on rented A100s demonstrates significant efficiency gains over recent generalist models. This suggests the training data and methodology are optimized for the VTON domain specifically, with no unnecessary scale.

## Implications

1. **Democratization of production VTON**: Open-source release eliminates vendor lock-in that has plagued VTON solutions. Developers can deploy, audit, and extend without reliance on proprietary APIs or restrictive licensing.

2. **Shift away from VAE latent space**: The pixel-space design validates that latent diffusion, while useful for general image generation, creates unnecessary bottlenecks for detail-sensitive tasks like garment transfer. This architectural decision likely influences future computer vision diffusion models.

3. **Maskless inference as a design principle**: Removing hard segmentation mask requirements simplifies pipelines and improves generalization. The model learning boundary inference implicitly is architecturally superior to explicit masking for variable garment volumes.

4. **Cost-effective domain specialization**: The training budget demonstrates that specialized models don't require massive compute. For organizations in e-commerce, fashion, or AR/VR, this is production-viable without building in-house or licensing enterprise solutions.

5. **Timing signal**: The release follows a human parsing model from the same team, suggesting deliberate building of a modular computer vision toolkit. This pattern indicates investment in tools for pixel-perfect body-related predictions.

## Related

- [GitHub: fashn-AI/fashn-vton-1.5](https://github.com/fashn-AI/fashn-vton-1.5) - Source code and weights
- [HuggingFace: fashn-ai/fashn-vton-1.5](https://huggingface.co/fashn-ai/fashn-vton-1.5) - Model hub hosting
- [Project Page: fashn.ai/research/vton-1-5](https://fashn.ai/research/vton-1-5) - Technical details and results
- [Prior Release: FASHN Human Parser](https://www.reddit.com/r/MachineLearning/comments/1qax221/p_opensourcing_a_human_parsing_model_trained_on/) - Companion human segmentation model
