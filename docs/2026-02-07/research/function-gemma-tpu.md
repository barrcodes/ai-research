# Fine-Tuning FunctionGemma on TPU

| | |
|---|---|
| **Source** | Hugging Face Blog |
| **URL** | [huggingface.co/blog/tengomucho/finetune-a-fitness-coach](https://huggingface.co/blog/tengomucho/finetune-a-fitness-coach) |
| **Researched** | 2026-02-07 |

## Overview

This article documents fine-tuning FunctionGemma 270M with LoRA on TPU v5litepod-8, achieving production-grade function-calling for a fitness coach application in under 10 minutes for $0.50. The key architectural insight is that TPU graph compilation requires static tensor shapes—a constraint that yields 6x training speedups when properly configured.

## Key Points

- **FunctionGemma function-calling architecture**: Uses four message roles (developer, user, assistant, tool) with structured tool_calls blocks. Minimal training data (213 examples) significantly reduces base model hallucinations through task-specific fine-tuning.

- **TPU-specific optimization stack**: SPMD initialization must precede model loading; FSDP v2 requires `xla_fsdp_v2=True` and `xla_fsdp_grad_ckpt=True` (NOT standard gradient checkpointing). GPU-trained configurations fail on TPU without these changes.

- **Static tensor shapes as the critical bottleneck**: Padding to fixed sequence length (`pad_to_multiple_of=max_length`) with `dataloader_drop_last=True` delivers 6x speedup by enabling single graph compilation. Without static shapes, TPU recompiles on every batch shape variance, increasing training time to 60+ minutes.

## Technical Details

**LoRA Configuration** targets attention projections where function-calling impact concentrates:
```python
LoraConfig(r=32, lora_alpha=64, lora_dropout=0.05,
           target_modules=["q_proj", "k_proj", "v_proj", "o_proj"])
```

**Critical TPU Configuration Sequence**:
```python
import torch_xla.runtime as xr
xr.use_spmd()  # Must call BEFORE model loading

fsdp_config = {
    "xla": True,
    "xla_fsdp_v2": True,              # Mandatory for TPU
    "xla_fsdp_grad_ckpt": True,       # TPU gradient checkpointing
}

sft_config = SFTConfig(
    pad_to_multiple_of=max_length,    # Static shapes → 6x speedup
    gradient_checkpointing=False,      # Disable (use xla_fsdp_grad_ckpt instead)
    dataloader_drop_last=True,
    **fsdp_training_args
)
```

**Performance**: ~10 minutes training, 213-example dataset, $0.50 cost on TPU v5litepod-8 (vs. GPU baseline requiring hours).

## Implications

**For practitioners**: This workflow demonstrates cost-effective function-calling customization for edge-deployable models (270M parameters). The 6x speedup from static shapes is architecturally significant—it shifts the optimization mindset from algorithmic efficiency to compiler-aware tensor layout.

**Design trade-offs**: LoRA + static padding sacrifice model capacity and memory efficiency for training speed and cost. Viable for specialized domain tasks but requires empirical validation that 213 examples suffice for your function set.

**Integration pattern**: FunctionGemma's structured message format with explicit tool_calls enables clean separation between model inference and tool orchestration—critical for production reliability.

**Scaling considerations**: The approach works for TPU v5 (and likely v6/v7) but requires template changes for different inference targets (GPU inference uses standard FSDP, not XLA variants).

## Sources

- [Hugging Face Blog: Fine-Tuning FunctionGemma on TPU](https://huggingface.co/blog/tengomucho/finetune-a-fitness-coach)
- [FunctionGemma Model Card](https://huggingface.co/google/functiongemma-270m-it)
- [Model: tengomucho/functiongemma-fitness-coach](https://huggingface.co/tengomucho/functiongemma-fitness-coach)
