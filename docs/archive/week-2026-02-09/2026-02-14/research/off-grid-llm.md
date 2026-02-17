# Off-Grid Edge LLM Deployment

| | |
|---|---|
| **Source** | MIT CSAIL, NVIDIA Developer, Learn OpenCV |
| **URL** | [hanlab.mit.edu/blog/tinychat](https://hanlab.mit.edu/blog/tinychat) |
| **Researched** | 2026-02-14 |

## Overview

Edge LLM deployment solves a critical problem: running capable AI models without cloud connectivity or latency dependencies. The primary technical challenge is memory bandwidth, not compute. Emerging solutions use 4-bit quantization combined with optimized inference engines to run 7-13B parameter models at 17-30 tokens/second on devices like Jetson Orin, enabling offline AI for robotics, medical devices, and mission-critical systems.

## Key Points

- **Memory bandwidth is the bottleneck, not compute**: Edge GPUs have sufficient floating-point capacity; the constraint is moving model weights from DRAM into cache for each token generation. TinyChat reduces DRAM traffic 4x through activation-aware weight quantization.

- **4-bit quantization preserves model quality**: AWQ (Activation-Aware Weight Quantization) analyzes activation magnitudes to identify which weights are critical, enabling safe 4-bit compression. Combined with SmoothQuant for activations, this approach maintains baseline accuracy while drastically reducing memory footprint.

- **Inference architecture matters more than hardware tier**: TinyChatEngine loads integer weights directly into on-chip SRAM and performs dequantization in-cache, eliminating the standard approach of moving full-precision data through main memory. This architectural choice yields 3.3x speedup versus FP16 baselines.

- **Practical hardware tiers exist**: Jetson Orin Nano (8GB) handles 3-7B models; RTX A6000 (48GB) runs quantized 70B models at 14 tok/s; standard deployment infrastructure (vLLM, Llama.CPP) works across all tiers with appropriate tuning.

## Technical Details

**Quantization Pipeline**
- Input: FP16 weights, FP32 activations
- Step 1: Activation-aware analysis identifies critical weight channels
- Step 2: 4-bit weight quantization with SmoothQuant
- Step 3: Dequantization in on-chip SRAM during inference
- Result: 40-50% memory reduction, 4x DRAM traffic reduction

**Performance Benchmarks**

| Model | Device | Throughput | Memory |
|-------|--------|-----------|--------|
| LLaMA-2 7B | Jetson Orin | 30 tok/s | ~4GB |
| LLaMA-2 13B | Jetson Orin | 17 tok/s | ~8GB |
| LLaMA-2 70B | IGX Orin + RTX A6000 | 14 tok/s | ~35GB |

**Deployment Stack**: vLLM handles batching and request scheduling on edge hardware. Holoscan SDK (NVIDIA) integrates sensor streams for multimodal inference. LangChain/Haystack enable application-level orchestration.

## Implications

**When to deploy at edge:**
- Offline operation required (field robotics, submarines, aircraft)
- Latency-sensitive tasks (sub-100ms requirement)
- Privacy-critical workloads (medical records, financial data, security)
- Cost optimization (avoid cloud egress charges for high-volume inference)

**Trade-offs to accept:**
- Model capacity: 7-13B practical ceiling for sub-$5k hardware; 70B requires $40k+
- Throughput reduction: Edge inference 3-10x slower than cloud A100 clusters
- Operational complexity: No autoscaling, manual capacity planning, debugging on constrained hardware

**Architectural decision points:**
- If model must stay offline: use quantized 7B variant with TinyChat
- If latency is critical but cloud fallback acceptable: hybrid model with local 7B + cloud 70B
- If data sensitivity is driver: prefer edge even with throughput penalty; sanitized APIs (LLaMA 2, Falcon) reduce compliance burden

**Emerging optimization vectors**: Knowledge distillation (training smaller models on larger ones) and speculative decoding (draft smaller model, verify with larger) can further improve edge throughput beyond quantization alone.

## Sources

- [TinyChat: Large Language Model on the Edge](https://hanlab.mit.edu/blog/tinychat) - MIT CSAIL approach to activation-aware quantization and on-chip dequantization
- [Deploy LLMs at the Edge with NVIDIA IGX Orin](https://developer.nvidia.com/blog/deploy-large-language-models-at-the-edge-with-nvidia-igx-orin-developer-kit/) - Hardware capabilities and medical AI use cases
- [Deployment on Edge: LLM Serving on Jetson using vLLM](https://learnopencov.com/deployment-on-edge-vllm-on-jetson/) - Practical setup for Jetson Orin Nano with production infrastructure
