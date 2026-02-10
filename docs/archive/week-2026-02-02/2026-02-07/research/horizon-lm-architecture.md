# Horizon-LM: A RAM-Centric Architecture for LLM Training

| | |
|---|---|
| **Source** | arXiv |
| **URL** | [arxiv.org/abs/2602.04816](https://arxiv.org/abs/2602.04816) |
| **Researched** | 2026-02-07 |

## Overview

Horizon-LM inverts conventional GPU-centric training by positioning host RAM as the authoritative parameter store and treating GPUs as transient compute caches. This CPU-master, GPU-template architecture enables single-GPU training of 120B-parameter models, achieving 12.2× higher throughput than DeepSpeed ZeRO-3 with CPU offloading while maintaining predictable memory scaling.

## Key Points

- **Architectural Inversion**: Parameters and optimizer states reside exclusively in host memory; GPUs host only reusable layer templates streamed on-demand
- **Layer-Contiguous Memory Tiling**: All transformer layer state (BF16 weights/gradients, FP32 optimizer moments) packed into 4KB-aligned blocks, enabling saturating PCIe DMA transfers with minimal overhead
- **Pipelined Triple-Stream Execution**: Compute, weight-transfer, and gradient-transfer streams overlap completely—forward parameters stream in while computation runs on prior layer, backward gradients offload immediately
- **Block-Wise Activation Checkpointing**: Sparse checkpoints replace full activation graphs; intermediates reconstruct just-in-time during backward pass without persistent GPU residence
- **Bounded Memory Invariants**: GPU memory scales with max-layer width and checkpoint interval (independent of depth); host memory scales linearly with parameters only

## Technical Details

**Memory Consolidation Strategy**
Pinned Slab Recycling maintains a fixed-capacity pool rather than pinning entire model—critical for avoiding OS page table depletion. Layer-contiguous packing transforms scattered tensor management into single-burst DMA operations saturating PCIe bandwidth.

**Execution Primitives**
Deterministic scheduling via explicit primitives (StreamIn → Bind → Compute → Offload) replaces framework-managed autograd graphs, eliminating hidden GPU state accumulation. Multi-stream event synchronization prevents buffer conflicts while maintaining continuous utilization.

**Precision Layout**
Mixed-precision strategy: BF16 for weights/gradients (reduced memory footprint), FP32 for Adam moments (numerical stability). This asymmetry cuts memory overhead compared to uniform precision.

**Performance Numbers**
- A100 baseline: 12.2× throughput vs. ZeRO-3 CPU offloading
- H200 system: 120B parameters reliably trainable on single GPU with 1.5TB host RAM
- Memory consumption bounded to theoretical parameter footprint

## Implications

**Architectural Significance**: This work redefines feasibility boundaries for node-scale training. Host memory, not GPU memory, becomes the primary constraint—shifting hardware investment calculus away from GPU-centric clusters toward mixed-capacity machines.

**Practical Trade-offs**: Gains throughput and capacity at the cost of explicit control—requires abandoning framework autograd graph management for manual gradient propagation. Development complexity increases; recomputation overhead is acceptable for parameter-bound workloads.

**When to Apply**: Ideal for single-GPU adaptation, fine-tuning large models, and research-lab training lacking multi-GPU clusters. Less suitable for pre-training where distributed parallelism extracts better overall efficiency. Primary value for practitioners with high-RAM machines but limited GPU memory.

**Competitive Positioning**: Complements rather than replaces multi-GPU systems—alternative for development and experimentation where ZeRO-style distributed training is overkill. Enables efficient parameter-per-GPU ratios on commodity hardware.

## Sources

- [Horizon-LM: A RAM-Centric Architecture for LLM Training (arXiv HTML)](https://arxiv.org/html/2602.04816v2) - Full paper with algorithm details
- [GitHub - DLYuanGod/Horizon-LM](https://github.com/DLYuanGod/Horizon-LM) - Reference implementation
- [Hacker News Discussion](https://news.ycombinator.com/item?id=46928890) - Community analysis
