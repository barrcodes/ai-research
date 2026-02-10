# CPU-Based MoE Inference: Practical Performance on Budget Hardware

| | |
|---|---|
| **Source** | r/LocalLLaMA |
| **URL** | [old.reddit.com/r/LocalLLaMA/comments/1qxcm5g/](https://old.reddit.com/r/LocalLLaMA/comments/1qxcm5g/no_nvidia_no_problem_my_2018_potato_8th_gen_i3/) |
| **Researched** | 2026-02-06 |

## Overview

A practitioner in Burma achieved 10 TPS on DeepSeek-Coder-V2-Lite (16B MoE) using a 2018 HP ProBook 650 G5 with 8th-gen Intel i3-8145U and 16GB dual-channel RAM. The post demonstrates that MoE architectures fundamentally change CPU inference feasibility—not through raw compute, but through dramatic reduction in active parameters per token (2.4B active of 16B total). This directly challenges the premise that competitive inference requires discrete GPUs.

## Key Points

- **MoE as architectural compression**: 16B MoE with 2.4B active parameters outperforms dense 3.8B-8B models at 4 TPS—the parameter reduction is the optimization, not the bottleneck
- **Dual-channel memory is mandatory**: Single-channel DDR4 setups will choke on bandwidth; going single→dual channel "doubles your throughput ceiling"
- **iGPU vs CPU trade-off**: Intel UHD 620 (OpenVINO backend) slightly beats CPU (9.0/10 vs 8.5/10 rating)—8.99 t/s peak vs 8.59 t/s—but introduces unpredictable issues
- **Memory bandwidth is the actual bottleneck**: Not compute. The i3-8145U runs at ~34-38 GB/s with DDR4-2133/2400, sufficient for these workloads but tight with 16GB ceiling
- **Linux is non-negotiable**: Windows background processes consume RAM needed for model layers; Ubuntu mandatory for squeezing every MB
- **OpenVINO compilation takes time**: iGPU initially appears hung during first-run kernel compilation; 1+ minute warmup expected
- **Language model drift observed**: Minor Chinese token leakage (~5 instances across 20 questions) on iGPU path, likely INT8/FP16 precision artifacts on older UHD 620
- **Quantization strategy**: Uses Q4_K_M GGUF (via llama-cpp-python) as OpenVINO file conversion is impractical on 16GB systems

## Technical Details

### Hardware Stack
```
CPU: Intel Core i3-8145U (2 cores, 4 threads, Iris Plus Graphics 620)
RAM: 16GB DDR4 dual-channel (critical)
OS: Ubuntu (tested Debian 13, reverted to Ubuntu for stability)
```

### Engine Configuration
**llama-cpp-python with OpenVINO backend** is the differentiator. Standard llama-cpp (CPU only) or Vulkan on iGPU actually underperforms. The command is:
```bash
CMAKE_ARGS="-DGGML_OPENVINO=ON" pip install llama-cpp-python
```

Key parameters for llama-cpp-python:
- `n_gpu_layers = -1` (offload all)
- `device = "GPU"` (force iGPU)

### Performance Metrics (20-question evaluation, real-time streaming)

| Backend | Average | Peak | Notes |
|---------|---------|------|-------|
| CPU (pure) | 8.59 t/s | 9.26 t/s | Solid, consistent, logic remains clean |
| iGPU (OpenVINO) | 8.99 t/s | 9.73 t/s | Warm-up required, minor token drift |

The iGPU wins but margin is marginal given complexity trade-off.

### Why Dense Models Lose
Tested dense models 3.8B–8B: peaked at 4 TPS. MoE architecture is *not just faster* but demonstrates superior reasoning density—16B MoE parameters with sparse activation pattern beats 2x dense models.

### Bandwidth Math
i3-8145U DDR4 nominal: 34–38 GB/s dual-channel
- Q4_K_M 16B MoE ≈ 10GB model size
- ~2.4B active params per token = acceptable working set for cache

## Implications

1. **Accessibility democratized**: Inference is now viable on corporate e-waste. The 2018 MacBook Pro, laptop from corporate bulk sales—these become viable inference platforms. This matters for regions with hard currency constraints.

2. **MoE reshapes inference economics**: The parameter sparsity means architects should *reconsider dense vs sparse trade-offs*. 16B MoE > 8B dense isn't a compromise; it's a fundamental superiority claim for constrained environments.

3. **Memory bandwidth, not compute, is the ceiling**: CPU inference bottleneck is not FLOPS—it's GB/s. For practitioners: upgrading from single→dual-channel RAM has more impact than upgrading CPU generations.

4. **OpenVINO is fragile but necessary**: No open-source alternative currently matches OpenVINO's iGPU integration for Intel hardware. Dependency management is complex; Windows support is questionable. But if you have Intel, it's the only realistic path to iGPU acceleration.

5. **Linux is non-fungible**: Not ideology—pragmatism. Windows background services eat RAM. On 16GB single-purpose systems, this is unacceptable. WSL2 may work but not tested.

6. **Production readiness issues**: Language drift (Chinese tokens) and compilation overhead suggest iGPU path is experimental. Reliability depends on workload tolerance for minor output anomalies. For pure determinism, CPU is safer.

7. **Model selection matters more than hardware**: Choosing MoE over dense is a 2x+ throughput multiplier. This dominates backend selection.

## Sources

- [r/LocalLLaMA Post: No NVIDIA? No Problem. My 2018 "Potato" 8th Gen i3 hits 10 TPS on 16B MoE](https://old.reddit.com/r/LocalLLaMA/comments/1qxcm5g/no_nvidia_no_problem_my_2018_potato_8th_gen_i3/) - Original post with performance benchmark data
- [esterzollar/benchmark-on-potato GitHub](https://github.com/esterzollar/benchmark-on-potato) - Referenced testing script (deep.py)
- DeepSeek-Coder-V2-Lite model architecture documentation for MoE parameter breakdown
