# AdaLLM: NVFP4-first Inference on RTX 4090

| | |
|---|---|
| **Source** | r/LocalLLaMA Community |
| **URL** | [old.reddit.com/r/LocalLLaMA](https://old.reddit.com/r/LocalLLaMA) |
| **Researched** | 2026-02-15 |

## Overview

AdaLLM is a specialized NVFP4-first inference runtime for Ada Lovelace architecture GPUs (RTX 4090) that achieves significant VRAM reduction through aggressive quantization while maintaining acceptable throughput. The system combines NVFP4 weight quantization with FP8 KV cache and custom decode kernels, reducing peak memory usage by approximately 240% with only 20-25% throughput degradation.

## Key Points

- **NVFP4 Quantization**: 4-bit floating point format achieves 3.5x memory reduction vs FP16, 1.8x vs FP8, with <1% accuracy loss on benchmarks
- **FP8 KV Cache**: Quantized key-value cache reduces memory footprint by 50% vs standard FP8, enabling context length doubling and batch size increases
- **Custom Decode Kernels**: Purpose-built kernels maintain FP8 precision throughout entire decode path including Gemma3 sliding-window attention
- **Model Coverage**: Supports Qwen3 (dense and MoE variants) and Gemma3 text models with native sliding-window attention handling
- **Memory Efficiency Trade-off**: Cuts peak VRAM by ~240% with realistic 20-25% throughput cost—acceptable for most local inference scenarios
- **Architecture Integration**: Loads NVFP4 weights via custom loader preserving scales in kernel mode; prefill uses FlashAttention (variable length), decode uses specialized FP8 kernel

## Technical Details

### Implementation Architecture

**Weight Loading**: NVFP4 weights loaded through `adallm.infra.loader` with scale preservation in kernel mode. This approach avoids the overhead of dequantization-before-compute by keeping scales available for the execution kernels.

**KV Cache Allocation**: Quantized to FP8 and attached directly to attention layers. The two-level scaling strategy in NVFP4 (fine-grained E4M3 + second-level FP32 scalar) enables high accuracy preservation despite extreme quantization.

**Compute Pipeline**:
- Prefill phase: FlashAttention with variable-length sequences
- Decode phase: Custom FP8 kernel maintaining precision throughout

### Performance Characteristics

**Memory Profile**: RTX 4090 with 24GB VRAM can now support larger models in quantized form. Example: Qwen3-8B-NVFP4 footprint approximately 4GB, enabling deployment of models previously requiring larger GPUs.

**Throughput Dynamics**: 20-25% throughput loss is primarily decode-bound due to FP8 operations. Prefill performance remains closer to baseline due to FlashAttention optimization.

**Accuracy Preservation**: NVFP4's two-level scaling maintains <1% degradation on key benchmarks including LiveCodeBench, MMLU-PRO, MBPP, and Ruler 64K.

## Implications

**For Local Deployment**: This shifts the economics of consumer GPU inference. A RTX 4090 with NVFP4 quantization becomes viable for production inference on moderately-sized models (8-70B effective parameters) without resorting to clustering or cloud inference.

**For Inference Stack**: Highlights the emerging importance of quantization-aware runtime design. Generic FP8 KV cache quantization isn't sufficient—domain-specific kernels (sliding-window attention, MoE routing) require specialized handling for correctness.

**For Model Architecture Compatibility**: Not all architectures benefit equally. Models with sliding-window attention (Gemma3) and MoE designs (Qwen3) require custom kernel implementations, suggesting future quantization runtimes must be architecture-aware.

**Training Implications**: The success of NVFP4 at <1% accuracy loss validates aggressive low-precision pretraining/post-training quantization as a viable path for future model compression, reducing the need for 8-bit intermediate representations.

**Competitive Pressure**: NVIDIA's Blackwell (native NVFP4 support in tensor cores) will accelerate adoption. This RTX 4090 implementation demonstrates the technique works on non-native hardware but hints at architectural limitations—Blackwell's native support will provide significantly better throughput.

## Sources

- [GitHub: BenChaliah/NVFP4-on-4090-vLLM](https://github.com/BenChaliah/NVFP4-on-4090-vLLM) - Official AdaLLM repository with implementation details
- [NVIDIA Blog: Introducing NVFP4 for Efficient and Accurate Low-Precision Inference](https://developer.nvidia.com/blog/introducing-nvfp4-for-efficient-and-accurate-low-precision-inference/) - NVFP4 technical overview and accuracy benchmarks
- [NVIDIA Blog: Optimizing Inference for Long Context with NVFP4 KV Cache](https://developer.nvidia.com/blog/optimizing-inference-for-long-context-and-large-batch-sizes-with-nvfp4-kv-cache/) - KV cache quantization technical details
- [vLLM Documentation: Quantized KV Cache](https://docs.vllm.ai/en/latest/features/quantization/quantized_kvcache/) - vLLM FP8 KV cache implementation reference
- [SqueezeBits Blog: vLLM vs TensorRT-LLM KV Cache Quantization](https://blog.squeezebits.com/vllm-vs-tensorrtllm-8-kv-cache-quantization-35079) - Performance comparison of quantization strategies
