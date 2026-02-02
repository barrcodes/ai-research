# Fine-Tuning FunctionGemma on TPU to Create a Virtual Fitness Coach

| | |
|---|---|
| **Source** | Hugging Face Blog |
| **URL** | [huggingface.co/blog/tengomucho/finetune-a-fitness-coach](https://huggingface.co/blog/tengomucho/finetune-a-fitness-coach) |
| **Researched** | 2026-02-02 |

## Overview

This article demonstrates a practical approach to fine-tuning a 270M parameter FunctionGemma model on Google Cloud TPU v5litepod-8 for function-calling tasks, achieving a complete training cycle in ~10 minutes for $0.50. The key innovation is static tensor shape optimization that eliminates XLA recompilation overhead—a TPU-specific concern that GPU-trained teams typically overlook.

## Key Points

- **Static Shape Optimization Delivers 6x Speedup**: Fixing tensor shapes via `pad_to_multiple_of` and dropping incomplete batches prevents XLA graph recompilation (~30-60s per unique shape). Without this, training takes ~60 minutes; with it, ~10 minutes.

- **TPU-Specific FSDP v2 Configuration Required**: `xla_fsdp_v2=True` and `xla_fsdp_grad_ckpt=True` are mandatory for distributed training on TPU—not standard across GPU frameworks. Gradient checkpointing must be disabled in the main config.

- **LoRA + Function-Calling Eliminates Hallucination**: The base FunctionGemma model hallucinates non-existent functions; 213 synthetic training examples map natural language queries to correct function calls, reducing errors significantly (tested: "sleep metrics" → correct `get_sleeping_minutes` vs. invented `get_sleep_metrics`).

- **Two-Stage Generation for Tool Use**: Model generates structured function calls (`<start_function_call>call:get_body_battery_level{}<end_function_call>`), executes them, then generates natural language responses—avoiding the need for separate extraction pipelines.

## Technical Details

**Training Setup**:
- Model: `google/functiongemma-270m-it` with LoRA (r=32, targets attention layers)
- Hardware: TPU v5litepod-8, bfloat16 dtype
- SPMD Mode: Required initialization via `torch_xla.runtime.use_spmd()` before model loading

**Critical Configuration**:
```python
sft_config = SFTConfig(
    pad_to_multiple_of=max_length,
    dataloader_drop_last=True,
    gradient_checkpointing=False,  # Must be False on TPU
)

fsdp_config = {
    "xla": True,
    "xla_fsdp_v2": True,           # TPU-specific
    "xla_fsdp_grad_ckpt": True,    # Not standard gradient_checkpointing
}
```

**Function Call Parsing**: Regex extraction of structured output format, with support for escaped arguments in tool calls. Two-stage generation reduces prompt complexity and improves accuracy over single-pass approaches.

## Implications

**For Practitioners**:
1. **TPU Cost Advantage for Iteration**: Sub-dollar fine-tuning makes rapid experimentation viable for small models. Re-training on dataset changes costs pennies.

2. **Static Shapes Are Non-Negotiable on TPU**: Dynamically-shaped batches (common in production) will degrade to unoptimized performance. Requires rethinking padding strategies vs. GPU workflows.

3. **Function-Calling Accuracy Scales with Examples**: 213 synthetic examples provided reasonable coverage for 7 fitness functions. Teams building tool-use systems should expect similar scaling—quality and coverage matter more than volume at this scale.

4. **FSDP v2 Adoption Friction**: GPU-trained teams migrating to TPU will need refactoring for distributed training. The framework differences (SPMD mode, XLA-specific config) are real; documentation variance is high.

5. **Practical Tradeoff**: TPU efficiency comes at the cost of learner curve steepness. Worth it for high-iteration workflows; less so for one-off fine-tuning.

## Sources

- [Hugging Face Blog: Fine-Tuning FunctionGemma on TPU](https://huggingface.co/blog/tengomucho/finetune-a-fitness-coach)
- [GitHub: tengomucho/fitnesscoach](https://github.com/tengomucho/fitnesscoach)
- [Fine-tuned Model: tengomucho/functiongemma-fitness-coach](https://huggingface.co/tengomucho/functiongemma-fitness-coach)
