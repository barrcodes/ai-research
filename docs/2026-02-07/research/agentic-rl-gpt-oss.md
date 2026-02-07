# Unlocking Agentic RL Training for GPT-OSS: A Practical Retrospective

| | |
|---|---|
| **Source** | HuggingFace / LinkedIn Engineering |
| **URL** | [huggingface.co/blog/LinkedIn/gpt-oss-agentic-rl](https://huggingface.co/blog/LinkedIn/gpt-oss-agentic-rl) |
| **Researched** | 2026-02-07 |

## Overview

LinkedIn engineers identified and solved three critical infrastructure problems preventing stable agentic RL training on open-source MoE models: PPO log-probability mismatches from non-deterministic expert routing, training-inference divergence in attention mechanisms, and memory bottlenecks in distributed expert materialization. The solutions enable 3-4x faster convergence and stable training on 20B+ parameter models with long-context agentic trajectories.

## Key Points

- **PPO On-Policy Integrity Fix**: Non-deterministic MoE expert routing violated the assumption that log-probabilities remain constant across dual forward passes. Solution: override stored `old_log_prob` with current computation when on-policy to eliminate spurious importance-sampling clipping that destabilized training.

- **Attention Sink Backward Pass**: Inference engines (vLLM, SGLang) use sink tokens to stabilize long-context attention, but training relied on FlashAttention-v2 without backward gradient support. Implementing sink-aware gradients through the softmax normalization prevented training collapse on instruction-following and multi-turn agentic tasks.

- **MoE Expert Materialization**: Hugging Face inference path duplicated hidden states across all experts (180 GB tensors on 139 GB GPUs), causing OOM. Patched to use sequential expert processing during training, combined with sequence parallelism to partition activations across GPU cluster.

## Technical Details

The attention sink backward pass is non-trivial. Sinks participate in softmax normalization but don't contribute to output—the gradient must flow through both the normalization factor and the token interactions:

```
∂L/∂S_h = -Σ P_{i,h} (Σ_j P_{ij} ∂L/∂S_{ij})
```

where P_{i,h} is sink attention probability and P_{ij} are content token weights.

Experimental validation on three distinct agentic tasks (GSM8K single-turn, VerifyIf instruction-following, ReTool multi-turn environment) showed attention was the bottleneck—freezing attention layers during training replicated reward curves of unfixed versions. Sequence parallelism reduces per-GPU activation memory from O(seq_len) to O(seq_len/sp_degree), enabling 16K+ context windows required for multi-step trajectories.

## Implications

**For practitioners building agentic systems:** This work unlocks stable RL training on open-source models at scale, eliminating infrastructure as a blocker. Requires FlashAttention-v3 with sink support (pending release) and FSDP configuration for expert routing. The attention-sink insight transfers to any long-context RL task; the MoE fixes are specific but generalizable to other distributed expert architectures.

**Architectural trade-off:** On-policy PPO with explicit log-prob override trades slight memory overhead (storing detached probabilities) for correctness—necessary for any model with stochastic components (routing, dropout). Sequence parallelism trades all-to-all communication at attention layers to reduce global activation memory, worthwhile for agentic workloads with expanding context windows.

**Production readiness:** Validated on GPT-OSS-20B and GPT-OSS-120B. Performance targets o3-mini/o4-mini class reasoning. Implementation in verl framework indicates community adoption pathway, though FlashAttention-v3 backward pass is still pending internal review.

## Sources

- [HuggingFace Blog - LinkedIn](https://huggingface.co/blog/LinkedIn/gpt-oss-agentic-rl) - Full technical retrospective with implementation details
- [arxiv:2510.11370](https://arxiv.org/pdf/2510.11370v1) - MoE router stabilization reference
- [arxiv:2309.17453](https://arxiv.org/abs/2309.17453) - Attention sinks foundational work
- [verl GitHub](https://github.com/volcengine/verl) - Training framework implementation
