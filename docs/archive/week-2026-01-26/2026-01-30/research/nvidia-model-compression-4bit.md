# Quantization-Aware Distillation for NVFP4 Inference Accuracy Recovery

| | |
|---|---|
| **Source** | NVIDIA/arXiv |
| **URL** | [arxiv.org/abs/2601.20088](https://arxiv.org/abs/2601.20088) |
| **Researched** | 2026-01-30 |

## Overview
NVIDIA released a technical report on Quantization-Aware Distillation (QAD) for recovering accuracy when compressing large language and vision-language models from 16-bit (BF16) floating-point to 4-bit (NVFP4) format. The approach maintains 99.4% accuracy on benchmark evaluations through knowledge distillation, demonstrating near-lossless compression for post-trained models that undergo complex multi-stage training pipelines including supervised fine-tuning (SFT), reinforcement learning (RL), and model merging.

## Key Points

- **NVFP4 Format Advantages**: 2-3× higher arithmetic throughput and 1.8× memory reduction compared to FP8, with a smaller block size (16 vs. 32 for MXFP4), per-block E4M3 scales, and second-level FP32 scaling for dynamic range
- **QAD Superior to QAT**: Uses KL divergence distillation loss rather than task-specific cross-entropy, achieving near-zero KL divergence (0.004) from the teacher model versus significant divergence in QAT (0.311)
- **Handles Complex Training Pipelines**: Excels where standard QAT fails—on models trained through multi-stage post-training, RL stages, and model merging, which are difficult to replicate exactly
- **Data Robustness**: Recovers accuracy without full training data access, enabling cross-domain transfer and working effectively with partial or domain-specific datasets
- **Evaluated on Production Models**: Tested on Nemotron 3 Nano 30B-A3B, Nemotron Nano 9B V2, AceReason Nemotron 1.1 7B, Llama Nemotron Super V1 49B, and Nemotron Nano 12B V2 VL
- **Benchmark Performance**: On Llama Nemotron Super V1, QAD achieves 94.6% on MATH500 (BF16: 95.8%), 45.6% on AIME25 (BF16: 46.0%), 64.5% on GPQA-D (BF16: 66.5%), 87.8% on IFEval-Instruction (BF16: 87.5%)

## Technical Details

### NVFP4 Format Innovation
- **Block Structure**: Smaller 16-element blocks with per-block E4M3 scales (non-power-of-two factors) versus traditional 32-element blocks
- **Dual-Level Scaling**: Per-block E4M3 scales plus per-tensor FP32 scale extends dynamic range and provides fine-grained adaptation to local data distributions
- **Hardware Alignment**: Optimized for Blackwell and newer NVIDIA architectures with native 4-bit floating-point support

### Quantization-Aware Distillation Method
```
Loss = D_KL(p_teacher || p_student) = Σ p_teacher(y|x) * log(p_teacher(y|x) / p_student(y|x))
```

The critical insight: QAD preserves the teacher's output distribution (achieving KL divergence ≈ 0.004) whereas QAT matches task-specific loss while diverging significantly from the teacher's distribution (KL divergence ≈ 0.311). This means QAD effectively acts as a "distribution-preserving" quantization rather than retraining.

### Key Advantages Over QAT

1. **RL Model Compatibility**: QAT fundamentally breaks RL-trained capabilities (AA-LCR drops from 35.9 to 24.8 on Nemotron 3 Nano 30B-A3B), while QAD recovers to 34.3, nearly matching the BF16 baseline of 35.9

2. **Multi-Stage Pipeline Handling**: No need to replicate complex pipelines with quantized forward passes or reproduce model merging procedures—apply QAD once to the final model

3. **Data Efficiency**: Works with partial domain data; for AceReason Nemotron trained on both math and code, using math-only data achieves nearly identical results to math+code combined

### Empirical Results on RL-Heavy Models
- **Nemotron 3 Nano 30B-A3B** (hybrid Mamba-Transformer with multi-stage RL):
  - AA-LCR: BF16=35.9, PTQ=31.3, QAT=24.8 (severe degradation), QAD=34.3
  - AIME25: BF16=89.1, QAD=87.9 (-1.2%)
  - GPQA-D: BF16=73.0, QAD=72.7 (-0.3%)

- **AceReason Nemotron 1.1 7B** (Qwen2.5-based, RL-specialized for math/code):
  - AIME24: BF16=73.0, PTQ=69.4, QAT=62.1, QAD=71.7 (-1.3%)
  - AIME25: BF16=63.5, QAD=62.0 (-1.5%)

### Implementation
- Gradients remain in full precision; only weights and activations quantized during forward pass
- Compatible with existing training frameworks: Megatron-LM, NeMo, and HuggingFace Transformers implementations provided
- Pre-quantized checkpoints available on Hugging Face for immediate deployment

## Implications

**For Infrastructure Engineers**: This represents a 2-4× compression ratio with practical accuracy preservation, enabling deployment of large models on memory-constrained hardware. The 1.8× memory reduction per model (e.g., 4B parameters becomes ~7.2GB instead of 12.8GB) compounds significantly at scale.

**For Model Teams**: Eliminates the engineering burden of replicating multi-stage training pipelines for post-hoc quantization. Can apply QAD to any post-trained model without access to original training data, reducing barriers to deployment.

**For RL-Based Systems**: Critical breakthrough—QAT degrades RL-learned behaviors irreversibly, but QAD preserves them. This is essential for reasoning models, code generation models, and any RL-optimized systems where task-specific retraining risks catastrophic performance loss.

**For Inference at Scale**: The approach is orthogonal to other optimization techniques (pruning, sparsity, distillation), enabling stacking. The data robustness property means models can be quantized in isolated deployment environments without replicating the training infrastructure.

**Architecture Decision**: For production systems, QAD provides a safer, more practical path than QAT for post-hoc model compression. The minimal KL divergence preservation suggests the quantized model is a mathematical substitute for the full-precision model rather than a relearned approximation, reducing deployment risk.

## Related
- [NVFP4 Checkpoints on Hugging Face](https://huggingface.co/nvidia) - Pre-quantized models ready for deployment
- [Megatron-LM QAD Implementation](https://github.com/NVIDIA/Model-Optimizer/tree/main/examples/llm_qad) - Code for applying QAD to new models
- [NVIDIA NVFP4 Blog](https://nvidia.com) - Hardware and format specification details
- [arXiv Paper PDF](https://arxiv.org/pdf/2601.20088) - Full technical report with additional benchmarks and ablations
