# Fine-Tuning FunctionGemma on TPU to Create a Virtual Fitness Coach

| | |
|---|---|
| **Source** | Hugging Face Blog |
| **URL** | [huggingface.co/blog/tengomucho/finetune-a-fitness-coach](https://huggingface.co/blog/tengomucho/finetune-a-fitness-coach) |
| **Researched** | 2026-02-06 |

## Overview

This article demonstrates production-grade fine-tuning of a 270M parameter function-calling model on Google TPU v5 for ~$0.50 and 10 minutes of training time. The key contribution is identifying three critical TPU-specific optimizations—SPMD initialization, FSDP v2 configuration, and static tensor shapes—that deliver 6x speedup over naive approaches. The work makes small model function-calling practical for domain-specific applications with explicit, auditable tool invocation patterns.

## Key Points

- **Static tensor shapes are the performance lever**: Forcing `pad_to_multiple_of=max_length` eliminates TPU graph recompilation overhead (30-60s per unique shape), delivering the majority of the 6x speedup. This differs fundamentally from GPU training where dynamic padding is acceptable.

- **FSDP v2 + SPMD is mandatory for TPU scaling**: Unlike GPU distributed training, TPU requires explicit `xr.use_spmd()` call before model initialization and TPU-specific FSDP flags (`xla_fsdp_v2=True`, `xla_fsdp_grad_ckpt=True`). Standard PyTorch Distributed won't work.

- **Function-calling reduces hallucination vs. natural language generation**: Fine-tuned model stops inventing non-existent functions (e.g., `get_sleep_metrics` that doesn't exist in the API). This structural constraint makes outputs inherently safer and more auditable than free-form text generation.

- **213 synthetic examples drive measurable improvement**: While not large-scale, domain-specific function-calling datasets are orders of magnitude smaller than typical LLM training, enabling sub-$1 production deployment.

## Technical Details

**TPU Configuration Differences from GPU**

| Aspect | TPU Requirement | GPU Alternative |
|--------|-----------------|-----------------|
| SPMD Mode | `xr.use_spmd()` before init (mandatory) | N/A |
| Gradient checkpointing | `gradient_checkpointing=False` + `xla_fsdp_grad_ckpt=True` | `gradient_checkpointing=True` |
| Tensor padding | `pad_to_multiple_of=max_length` (critical) | Optional dynamic padding OK |
| Graph compilation | Static shapes required | Dynamic shapes cached |

**Function-Calling Format**

FunctionGemma expects three-role conversation structure:
- `developer`: System prompt with available functions and signatures
- `user`: Natural language query
- `assistant`: Structured tool calls with parsed results

Example invocation:
```
<start_function_call>call:get_body_battery_level{}<end_function_call>
```

**Cost/Performance Numbers**
- Training: 10 minutes at $2.40/hour TPU v5litepod-8 = ~$0.40
- Model size: 270M parameters, deployable on edge devices
- Dataset: 213 examples for initial training; 1,000+ recommended for robustness

## Implications

**Architectural Advantages**
- Small function-calling models shift from "can we afford to fine-tune?" to "why aren't we?"—sub-minute training iteration cycles enable rapid API schema evolution
- Explicit function calls replace probabilistic text generation, eliminating hallucinated API endpoints and enabling runtime verification
- No dependency on local inference infrastructure; 270M model fits on-device for latency-sensitive applications

**Trade-offs and Constraints**
- **Multi-turn limitations**: 270M model struggles with extended conversational context; larger models (Qwen 0.6B+) or specialized tool-calling models (Falcon-H1-Tiny) needed for complex scenarios
- **TPU lock-in**: Optimizations are TPU-specific. GPU or other accelerators require different FSDP/gradient checkpointing strategies
- **API complexity ceiling**: Current approach limited to simple return values; structured APIs with complex nested objects require additional processing

**When to Use This Approach**
- Fixed API schemas with deterministic function sets (fitness trackers, internal tools, device controllers)
- Cost-sensitive deployments where inference frequency justifies fine-tuning investment
- Audit/compliance requirements where tool invocation must be explicit and traceable

**Alternatives**
- Few-shot prompting (Qwen/Claude models) if latency permits external API calls
- Larger foundation models (7B+) if you need multi-turn conversation and broader reasoning
- Specialized tool-calling models (tiiuae/Falcon-H1-Tiny) if you need out-of-box function-calling without fine-tuning

## Sources

- [tengomucho/finetune-a-fitness-coach](https://github.com/tengomucho/fitnesscoach) - Reference implementation with full training code
- [FunctionGemma Model](https://huggingface.co/tengomucho/functiongemma-fitness-coach) - Published fine-tuned checkpoint
- [Fitness Coach Function-Calling Dataset](https://huggingface.co/datasets/tengomucho/fitness-coach-function-calling) - 213 labeled examples
