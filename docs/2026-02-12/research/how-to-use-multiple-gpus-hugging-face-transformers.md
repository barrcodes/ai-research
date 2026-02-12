# How to Use Multiple GPUs in Hugging Face Transformers: Device Map vs Tensor Parallelism

| | |
|---|---|
| **Source** | Hugging Face Transformers Documentation |
| **URL** | [huggingface.co/docs/transformers/en/perf_infer_gpu_multi](https://huggingface.co/docs/transformers/en/perf_infer_gpu_multi) |
| **Researched** | 2026-02-12 |

## Overview

Hugging Face Transformers now supports two fundamentally different approaches for distributed GPU inference: **device mapping** (layer-level distribution across heterogeneous devices) and **tensor parallelism** (fine-grained weight matrix splitting with synchronized computation). Device mapping targets memory-constrained scenarios with flexible device targets; tensor parallelism maximizes throughput on homogeneous GPU clusters through coordinated computation.

## Key Points

- **Device Mapping** (`device_map="auto"`) distributes complete model layers sequentially across GPUs, CPU RAM, and disk storage based on available capacity. No synchronization overhead between layers, but only one GPU computes at a time—idle parallelism.

- **Tensor Parallelism** (`tp_plan="auto"`) slices weight matrices across GPUs at computation boundaries. All GPUs work simultaneously on each forward pass with strategic sharding (column-wise for Q/K/V projections, row-wise for output projections) and all-reduce synchronization points.

- **Tensor parallelism constraints**: TP size must not exceed number of attention heads; attention heads and FFN hidden dimensions must be divisible by TP size. This limits scalability but prevents single-head splitting bottlenecks.

- **Framework integration**: Both approaches use PyTorch's `DeviceMesh` and `DTensor` for distributed abstractions. Tensor parallelism registers partitioning strategies (`colwise`, `rowwise`, `sequence_parallel`, etc.) with automatic or manual `tp_plan` specification.

- **Memory efficiency trade-off**: Device mapping requires fewer inter-GPU transfers but leaves GPUs idle sequentially. Tensor parallelism requires synchronous communication (all-reduce at output layers) but maximizes parallelism efficiency within a machine.

## Technical Details

**Device Mapping Mechanism**

Device mapping automatically places model layers on optimal devices using `load_checkpoint_and_dispatch()`:
- GPU memory fills first to maximum capacity
- Overflow spills to CPU RAM
- Remaining weights use memory-mapped disk access
- Hooks automatically move activations/weights to execution device during forward pass
- Supports `max_memory` constraints per device

**Tensor Parallelism Architecture**

Transformers implements TP via weight matrix slicing in linear layers:

| Layer Type | Strategy | Synchronization |
|---|---|---|
| Q/K/V projections | Column-wise (each GPU computes full projection × assigned columns) | None (local computation) |
| Attention output (W_O) | Row-wise (each GPU × its weight shard, then all-reduce) | All-reduce across GPUs |
| FFN first layer | Column-wise | None |
| FFN second layer | Row-wise | All-reduce |

Column-wise partitioning avoids communication; row-wise requires all-reduce to aggregate partial products. Run distributed inference with:

```bash
torchrun --nproc-per-node 4 inference_script.py
```

**Custom Partitioning**

Manual `tp_plan` specifies strategies per layer (example for Llama):

```python
tp_plan = {
    "model.layers.*.self_attn.q_proj": "colwise",
    "model.layers.*.self_attn.o_proj": "rowwise",
    "model.layers.*.mlp.up_proj": "colwise",
    "model.layers.*.mlp.down_proj": "rowwise",
}
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Meta-Llama-3-8B-Instruct",
    dtype=torch.bfloat16,
    tp_plan=tp_plan
)
```

Packed weight strategies (`PackedColwiseParallel`, `PackedRowwiseParallel`) handle fused linear layers (e.g., gate_up_proj in MoE models).

## Implications

**When to use Device Mapping:**
- Single GPU doesn't fit model; need heterogeneous memory pool (GPU + CPU + disk)
- Inference only (training not supported with offload)
- Latency-tolerant workloads; sequential layer execution acceptable
- Cost-conscious deployments avoiding homogeneous GPU clusters

**When to use Tensor Parallelism:**
- High-throughput serving on homogeneous GPU cluster (4-8 A100s/H100s)
- Latency-critical applications; all GPUs compute per forward pass
- Models where TP constraints align (attention heads divisible by TP size)
- Single-node deployment (degrades across nodes due to inter-node bandwidth)

**Architecture Decision Points:**
- Device mapping scales to unlimited layer count but GPU utilization degrades
- Tensor parallelism limited by attention head count; combine with pipeline parallelism for full-scale training beyond single node
- Disk offload (device mapping) extremely slow without NVMe hardware
- Tensor parallelism communication overhead dominates inter-node; use for intra-node only

**Practical Constraint:** 70B parameter models need 280GB FP32 (140GB BF16). A100 80GB GPU requires TP with 4+ GPUs or device mapping across multiple machines.

## Sources

- [Hugging Face Transformers: Tensor Parallelism](https://huggingface.co/docs/transformers/en/perf_infer_gpu_multi) - Official documentation with partitioning strategies and benchmarks
- [Tensor Parallelism in Transformers: 5 Minutes to Understand](https://huggingface.co/blog/qgallouedec/tp) - High-level conceptual explanation of TP mechanics
- [Accelerate: Loading Big Models into Memory](https://huggingface.co/docs/accelerate/concept_guides/big_model_inference) - Device mapping API and memory-mapped disk offload details
- [Transformers Multi-GPU Inference](https://huggingface.co/docs/transformers/v4.47.0/perf_infer_gpu_multi) - Current inference parallelism guide
