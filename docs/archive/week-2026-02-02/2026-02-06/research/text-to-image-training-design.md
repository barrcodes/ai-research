# Training Design for Text-to-Image Models: Lessons from Ablations

| | |
|---|---|
| **Source** | Hugging Face / Photoroom Blog |
| **URL** | [huggingface.co/blog/Photoroom/prx-part2](https://huggingface.co/blog/Photoroom/prx-part2) |
| **Researched** | 2026-02-06 |

## Overview

Photoroom's PRX model training documentation systematically ablates diffusion-based text-to-image architecture choices across tokenizer alignment, training objectives, sparsification, and data strategies. The work identifies that **tokenizer learnability improvements yield larger gains than intermediate feature alignment**, contradicting intuitions from supervised learning. Token routing (TREAD) dramatically improves high-resolution training, and pixel-space training via x-prediction enables stable 1024×1024 generation at tolerable throughput.

## Key Points

- **REPA-E-VAE reshaping improves FID by 6 points**—larger than any alignment-only approach—by making the tokenizer learnable during training rather than frozen. FLUX2-AE achieves similar quality but at 55% throughput cost.

- **DINOv3-supervised alignment (REPA) drives early-stage improvements** but should be disabled after ~200K steps to avoid constraining late-stage learning. DINOv2 offers better efficiency trade-off for production.

- **JiT (x-prediction) enables pixel-space training** at 1024×1024 without VAE tokenization—only ~3× slower than 256² latent training with improved DINO metrics. Removes tokenizer bottleneck for high-fidelity generation.

- **Token routing (TREAD) is resolution-dependent**—negligible benefit at 256² but achieves 4.8-point FID gain + 23% speedup at 1024². Dense early/late + sparse middle layers optimal for transformer depth.

- **Long descriptive captions outperform short ones by massive margin** (18.2 FID vs 36.84)—counterintuitively, richer constraints reduce ambiguity and strengthen the learning signal. Suggests training strategy: long captions first, then fine-tune on mixed lengths.

- **Synthetic vs. real data split**: Synthetic (Midjourney) bootstraps composition quickly but produces synthetic textures; real data (Pexels) achieves photorealism. Combined approach optimal.

- **Muon optimizer outperforms AdamW** (15.55 vs 18.20 FID) with cleaner training dynamics—pre-conditioned first-order computation. Currently DDP-only in official PyTorch.

- **Critical precision bug**: Storing weights in BF16 (not just computing) degrades FID by 3.7 points. Rule: FP32 weights + BF16 autocast for forward/backward. Particularly sensitive: LayerNorm, attention softmax, RoPE, optimizer state.

## Technical Details

### Tokenizer-Centric Design

Most gains derive from modifying the VAE/AE itself rather than training objectives:

| Configuration | FID | CMMD | DINO-MMD | Speed |
|---|---|---|---|---|
| Baseline (frozen tokenizer) | 18.2 | 0.41 | 0.39 | 3.95 batches/sec |
| REPA-E-VAE (learnable) | **12.08** | 0.26 | 0.18 | 3.39 |
| FLUX2-AE (learnable) | **12.07** | 0.09 | 0.08 | 1.79 |

The implication: Pre-training the tokenizer is architectural—it's not just a feature extractor.

### Resolution-Aware Token Routing

At low resolution, routing overhead dominates. At high resolution (1024²), selective computation compounds:

```
TREAD @ 1024²: 14.10 FID (+3.3 vs baseline), 1.64 batches/sec (+23%)
SPRINT @ 1024²: 16.90 FID (+0.5 vs baseline), 1.89 batches/sec (+42%)
```

TREAD (learned routing) > SPRINT (fixed 75% drop) for quality. SPRINT preferred if latency/energy critical.

### Contrastive Flow Matching

Marginal gains (0.41→0.40 CMMD) with low overhead. Works better for discrete classification (ImageNet) than continuous text conditioning where classes naturally cluster differently. Not recommended for default recipe.

### Caption Design Trade-offs

Long captions (50+ tokens): 18.2 FID
Short captions (5-10 tokens): 36.84 FID

Explanation: Long captions reduce conditional distribution variance. Training learns easier from bounded hypothesis spaces. Practical strategy: Phase 1 (long), Phase 2 (mixed lengths).

### Synthetic Data Bootstrap

1M synthetic (Midjourney) achieves 18.2 FID on Unsplash eval, matching real data composition but with visible synthetic artifacts. 1M real Pexels yields 16.6 FID with photorealism. Mixed training: synthetic for structure/speed, real for texture.

## Implications

**For practitioners building production models:**

1. **Invest in tokenizer quality**—it's the first and most impactful lever. If using frozen tokenizers (Flux, Stable Diffusion), representation alignment (REPA) is the next best option but yields smaller gains. Learnable tokenizers should be your default architecture assumption.

2. **Pixel-space training is now viable** at moderate resolutions (1024²). If you need > 2K pixel generation without cascading approaches, JiT eliminates VAE bottlenecks at ~3× cost. Trade-off: cleaner architecture, no VAE artifacts, but slower.

3. **Token routing adoption depends on resolution**. Skip for 512² and below. At 1024² and higher, TREAD's 23% speedup justifies complexity. SPRINT if memory/energy is primary constraint over quality.

4. **Data preparation: long captions matter more than dataset size**. The 18-point FID collapse with short captions suggests caption quality >> image count. Curate aggressively (Alchemist SFT: 3,350 images with 20K steps yielded visible improvements). Synthetic bootstrap strategy reduces real data requirements.

5. **Optimizer choice impacts trajectory, not just final quality**. Muon's pre-conditioning produces cleaner convergence and better metrics. If unavailable, AdamW with careful LR tuning (1e-4) suffices.

6. **Precision tuning is model-critical**. The 3.7-point FID regression from BF16 weight storage is non-obvious and breaks silent numerics. Audit training configs for autocast scope.

**Architectural decisions to avoid:** Contrastive FM adds complexity without proportional quality gain in text-to-image settings. iREPA (spatial tricks) shows inconsistent benefits—simpler REPA is more robust.

## Sources

- [Hugging Face Blog - Photoroom PRX Part 2](https://huggingface.co/blog/Photoroom/prx-part2) - Comprehensive ablation study of diffusion model training techniques
