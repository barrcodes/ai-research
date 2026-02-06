# Performant Local Mixture-of-Experts CPU Inference with GPU Acceleration

| | |
|---|---|
| **Source** | Hugging Face Blog |
| **URL** | [huggingface.co/blog/Doctor-Shotgun/llamacpp-moe-offload-guide](https://huggingface.co/blog/Doctor-Shotgun/llamacpp-moe-offload-guide) |
| **Researched** | 2026-02-06 |

## Overview

This article provides a comprehensive technical guide for running large mixture-of-experts (MoE) models locally using llama.cpp with hybrid CPU/GPU execution. It covers architectural understanding of MoE models, principled weight offloading strategies, batch size optimization for prompt processing, advanced fork-specific optimizations, and multi-socket CPU considerations—enabling practitioners to run 600B+ parameter models (DeepSeek V3, GLM 4.X, Kimi K2) within practical hardware constraints.

## Key Points

- **MoE Architecture Decomposition**: MoE models contain always-active components (Attention, Dense FFN, Shared expert FFN) plus routed experts that activate selectively. Crucially, total parameters (671B) differ from active parameters (37B), enabling large model deployment on constrained hardware by keeping active-per-token weights on the GPU while routing sparse expert layers to CPU RAM.

- **GPU-CPU Layer Assignment Strategy**: Maximize GPU utilization by placing all always-active components plus as many routed expert layers as VRAM permits on GPU. Use regex-based tensor override syntax (`-ot`) to explicitly map layers to devices with precision, avoiding suboptimal default distributions.

- **Disaggregated Prompt Processing Sensitivity**: CPU+GPU inference performance is hypersensitive to batch size configuration. The GPU performs disaggregated prompt processing (copying CPU-resident weights to GPU) only above a threshold (32 tokens default). Practical configurations require `-b 4096 -ub 4096` (logical and physical batch sizes) versus pure GPU defaults (512), with batch size tuning against VRAM constraints for compute buffers.

- **ik_llama.cpp Fork Advantages**: The fork implements CPU/CUDA hybrid-specific optimizations: graph-mode split scheduling for true multi-GPU parallelism (not sequential layer alternation), multi-head latent attention (MLA) for DeepSeek architectures, and graph reuse—measurably faster for multi-GPU systems despite potential hardware variance.

- **NUMA Awareness for Multi-Socket CPUs**: Default llama.cpp doesn't optimize cross-socket memory access. Binding workloads to single NUMA nodes, interleaving with numactl, and combining with mmap-based tensor migration can recover significant throughput, though prompt processing speed degrades with disaggregated offloading when tensors are distributed.

## Technical Details

### Weight Offloading Configuration

The core optimization principle assigns components by computation pattern, not uniform distribution:

```
./llama-server \
-m <model.gguf> \
-ngl 999 \
-ot "blk\.([0-9]|[1-2][0-9]|30)\.=CUDA0,exps=CPU"
```

This maps layers 0-30 (including always-active attention+FFN) to GPU with regex priority, leaving higher-numbered routed experts on CPU. The `-ot` tensor override system allows fine-grained control over layer distribution, supporting multi-GPU assignment via per-GPU regex (e.g., `blk\.(0-9)\.=CUDA0,blk\.(10-19)\.=CUDA1`).

Key insight: The `--n-cpu-moe` flag counts from highest layers (opposite of explicit assignment), creating potential discrepancies with Dense FFN layers typically at model start (DeepSeek V3 layers 0-2).

### Batch Size Break-Even Analysis

Disaggregated prompt processing trades GPU bandwidth for compute gain. With 300GB CPU-resident weights and PCIe 4.0 x16 (~16 GB/s effective), weight copying requires ~10 seconds—necessitating batch sizes of several hundred tokens to amortize the transfer cost. The `GGML_OP_OFFLOAD_MIN_BATCH` environment variable overrides defaults (32) for low-PCIe-bandwidth or high-CPU-weight scenarios.

### Multi-GPU Graph Scheduling (ik_llama.cpp)

Layer-mode split (default) alternates GPU execution sequentially, utilizing one GPU per forward pass. Graph-mode split (`-sm graph`) distributes tensors across GPUs for simultaneous computation, approximating tensor parallelism:

```
-sm graph -ot "blk\.(19|[2-9][0-9])\.ffn_(up|gate|down)_exps\.weight=CPU"
```

This inverts the assignment paradigm: specify CPU-bound layers only, letting `-ngl 999` distribute remaining layers evenly. Requires NCCL for peer-to-peer optimization and driver support for direct GPU access.

### NUMA Tensor Migration

Dropping page cache (`sudo sh -c "sync; echo 1 > /proc/sys/vm/drop_caches"`) combined with mmap and `--numa distribute` triggers NUMA-aware tensor placement on warm-up, improving token generation speed significantly but degrading prompt processing when tensors scatter across nodes. The provided wrapper scripts (`disable-numa-balancing.sh`, `numactl-bind-socket.sh`) manage NUMA balancing state and binding context.

## Implications

**For local inference practitioners:**
- MoE models are now practically deployable on gaming PCs and workstations via sparse activation + strategic offloading. The gap between edge and data center deployment narrows significantly.
- GPU memory is the primary bottleneck; CPU RAM is abundant. Optimize for GPU occupancy first (always-active layers), then prompt batch sizing (PCIe bandwidth).
- Fork selection matters: ik_llama.cpp's graph scheduling provides measurable multi-GPU gains, but empirical benchmarking with `llama-sweep-bench` is mandatory—hardware variance is substantial.

**For system architects:**
- Batch size tuning is non-obvious: higher batches improve GPU utilization but require VRAM; empirical breakeven determination is required per hardware configuration.
- NUMA-aware deployment on multi-socket servers requires explicit configuration; default behavior is pessimal. Tensor migration via mmap offers an unconventional but effective optimization.
- The disaggregated prompt processing mechanism creates a fundamental tradeoff: large prompt batches (beneficial for throughput) require massive GPU bandwidth when CPU weights are offloaded, making token-generation-only workloads more practical than batch-heavy prompt processing.

**For model selection:**
- Quantization type consistency matters (Q, K, V must match for `--merge-qkv`). Smaller quantizations (Q4_K_M) fit more experts on GPU, improving throughput proportionally.

## Sources

- [Hugging Face Blog: Performant Local Mixture-of-Experts CPU Inference](https://huggingface.co/blog/Doctor-Shotgun/llamacpp-moe-offload-guide) - Comprehensive guide by Doctor Shotgun and Geechan
- [ik_llama.cpp Repository](https://github.com/ikawrakow/ik_llama.cpp) - Optimized fork for CPU/CUDA hybrid inference
- [llama.cpp Issues #17026](https://github.com/ggml-org/llama.cpp/issues/17026#issuecomment-3492543006) - Break-even analysis discussion by Jukofyork
- [ik_llama.cpp Pull Request #698](https://github.com/ikawrakow/ik_llama.cpp/pull/698#issue-3327278619) - MLA batch threshold documentation
