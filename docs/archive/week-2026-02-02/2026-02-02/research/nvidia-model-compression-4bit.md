# NVIDIA Model Compression: 16-bit to 4-bit with 99.4% Accuracy

| | |
|---|---|
| **Source** | NVIDIA Research / arXiv |
| **URL** | [research.nvidia.com/labs/nemotron](https://research.nvidia.com/labs/nemotron/) |
| **Researched** | 2026-02-02 |

## Overview

NVIDIA's NVFP4 format with Quantization-Aware Distillation (QAD) enables compressing large language models from 16-bit (BF16) to 4-bit floating-point while maintaining near-lossless accuracy (99.4% retention). This represents a 3.5x reduction in model memory footprint and 2-3x higher throughput compared to FP8, achieved through two-level micro-block scaling and knowledge distillation techniques designed specifically for post-trained LLMs with complex training pipelines.

## Key Points

- **NVFP4 Format**: A 4-bit floating-point format (E2M1 structure) with two-level scaling: fine-grained E4M3 (FP8) per-block scaling and second-level FP32 per-tensor scaling. Block size reduced from 32 (MXFP4) to 16 values, enabling more localized adaptation to data distributions
- **Quantization-Aware Distillation (QAD)**: Uses KL divergence loss to align a quantized student model with a full-precision teacher, proving more effective than traditional QAT for models with multi-stage post-training (SFT, RL, merging)
- **Accuracy Recovery**: Achieves near-BF16 performance across diverse models (Llama Nemotron, DeepSeek-R1) with less than 1% degradation on most benchmarks; particularly effective for reasoning tasks
- **Memory & Throughput**: 3.5x memory reduction vs FP16, 1.8x reduction vs FP8; 2-3x higher arithmetic throughput on NVIDIA Blackwell Tensor Cores
- **Data Efficiency**: QAD is robust to incomplete data coverage, enabling accuracy recovery without full original training data access

## Technical Details

### NVFP4 Scaling Architecture

NVFP4's key innovation is its two-level scaling strategy addressing the fundamental challenge of maintaining numerical accuracy across wide dynamic ranges in ultra-low precision:

**Block-level E4M3 Scaling**: Each 16-value block uses an E4M3 FP8 scale factor (vs. power-of-two E8M0 in MXFP4). E4M3 enables non-power-of-two fractional scaling factors, reducing quantization error by optimizing each block's scale collectively rather than snapping to nearest power-of-two. Mean squared error typically ~0.08 (E4M3) vs ~0.72 (E8M0) for scale factor encoding.

**Tensor-level FP32 Scaling**: A second-level per-tensor FP32 (E8M23) scale extends dynamic range and normalizes the tensor distribution so per-block E4M3 factors remain within their optimal range.

This hierarchical approach succeeds where simpler 4-bit formats fail: smaller block size (16 vs 32) enables finer-grained local adaptation; E4M3 precision eliminates coarse quantization artifacts; FP32 global scaling prevents block saturation.

### Quantization-Aware Distillation Method

QAD distinguishes itself from standard QAT by using soft-label distillation rather than task-specific loss:

**Loss Function**: Minimizes KL divergence between teacher and student logits:
- QAT: Cross-entropy on target task (next-token prediction for LLMs)
- QAD: KL divergence matching teacher's output distribution

**Critical Finding**: In experiments with Llama Nemotron Super v1, QAT achieved near-identical cross-entropy loss to BF16 (0.408 vs 0.408) but exhibited KL divergence of 0.311 vs baseline zero. QAD achieved KL divergence of 0.004—essentially preserving the full-precision model's output distribution while QAT acted as an additional post-training stage that altered model behavior.

**Engineering Advantages**:
- Works across multi-stage pipelines (SFT → RL → merging) without replicating each stage
- Stable training compared to QAT on RL-trained models (QARL remains unexplored)
- Robust to partial/lower-quality data; enables cross-domain knowledge transfer

### Empirical Performance

**DeepSeek-R1-0528 (FP8 vs NVFP4 accuracy)**:
- MMLU-PRO: 85% → 84%
- GPQA Diamond: 81% → 80%
- Math-500: 98% → 98%
- AIME 2024: 89% → 91%

**Llama Nemotron Super v1 (with QAD)**:
- MATH500: 95.8% (BF16) → 94.6% (NVFP4 QAD)
- AIME25: 46.0% → 45.6%
- GPQA-D: 66.5% → 64.5%
- QAT baseline on same tasks: 94.3%, 41.5%, 63.3% (notably worse on reasoning)

**Nemotron Nano v2 (with QAD)**:
- MATH500: 97.8% → 97.2%
- AIME25: 71.1% → 71.5% (exceeds teacher)
- GPQA-D: 64.0% → 62.7%

## Implications

**For Inference Infrastructure**: 3.5x memory reduction enables running larger models on same hardware or same models on significantly cheaper GPUs (relevant for on-prem and edge deployments). 2-3x throughput boost directly improves token/second metrics, reducing operational costs in production serving.

**For Model Deployment**: QAD's data robustness is architecturally significant—practitioners can quantize open-weight models without access to original training data/procedures. This decouples deployment optimization from training pipeline complexity, enabling third-party model compression at scale.

**For Multi-Stage Post-Training**: The distinction between QAT and QAD reveals a subtle but critical architectural property: QAT on RL-trained models risks degrading learned behaviors (evidenced by distribution shift), while QAD preserves learned policy/reasoning without engineering complexity of QARL.

**Trade-offs & Limitations**:
- Requires full-precision teacher model during distillation (not needed for inference)
- Training cost: distillation still requires backward passes, though simplification over QAT in complex pipelines may be net positive
- Performance varies by model size—small models benefit more from QAD; large models (70B+) show acceptable PTQ accuracy
- Reasoning tasks show slightly larger accuracy drops than language understanding tasks

**Architectural Significance**: NVFP4 represents the maturation of micro-scaled floating-point formats. The shift from MXFP4's power-of-two simplicity to E4M3's fractional precision reflects understanding that optimal quantization balances hardware efficiency (E2M1 base) with data distribution realism (E4M3 scaling). This pattern likely extends to future sub-4-bit formats.

## Sources

- [Introducing NVFP4 for Efficient and Accurate Low-Precision Inference](https://developer.nvidia.com/blog/introducing-nvfp4-for-efficient-and-accurate-low-precision-inference/) - NVIDIA Technical Blog, June 2025
- [Quantization-Aware Distillation for NVFP4 Inference Accuracy Recovery](https://arxiv.org/html/2601.20088) - arXiv:2601.20088, January 2026
- [NVFP4 Trains with Precision of 16-Bit and Speed and Efficiency of 4-Bit](https://developer.nvidia.com/blog/nvfp4-trains-with-precision-of-16-bit-and-speed-and-efficiency-of-4-bit/) - NVIDIA Technical Blog
- [Quantization-Aware Distillation for NVFP4 Inference Accuracy Recovery (PDF)](https://research.nvidia.com/labs/nemotron/files/NVFP4-QAD-Report.pdf) - NVIDIA Research Report
- [Enable NVFP4 Inference for Nemotron with Quantization-Aware Distillation](https://research.nvidia.com/labs/nemotron/nemotron-qad/) - NVIDIA Nemotron Project
