# Custom Kernels for All from Codex and Claude

| | |
|---|---|
| **Source** | Hugging Face Blog |
| **URL** | [huggingface.co/blog/custom-cuda-kernels-agent-skills](https://huggingface.co/blog/custom-cuda-kernels-agent-skills) |
| **Researched** | 2026-02-13 |

## Overview

HuggingFace successfully demonstrated autonomous CUDA kernel generation by packaging domain expertise into reusable agent skills (~550 tokens of structured guidance) that enable Claude and Codex to produce production-grade kernels with correct PyTorch bindings, build configurations, and benchmarks. The approach achieves 1.88x–1.94x speedups on RMSNorm kernels while reducing dependency on specialized kernel engineers.

## Key Points

- **Agentic skill abstraction**: Domain knowledge encapsulated as portable agent skills that teach coding agents to autonomously generate kernels targeting specific GPUs (H100, A100, T4) with proper PyTorch integration
- **Autonomous generation workflow**: Agents handle architecture parameter selection, CUDA source generation, C++ binding creation, build configuration, and benchmark validation end-to-end
- **Performance results**: RMSNorm kernels achieve 1.88x speedup on LTX-Video (diffusers) and 1.94x on Qwen3-8B (transformers) in isolation; end-to-end improvements are modest (1.06x–1.43x) because normalization is not the compute bottleneck
- **Distribution model**: Kernels publish to Hub with zero-compilation loading via binary caching, abstracted behind simple Python APIs
- **Portability**: Skills are portable across Claude Code, Codex, Cursor, and other Agent Skills-compliant tooling

## Technical Details

**Skill structure** encodes GPU-specific optimization (shared memory, bandwidth profiles), library integration patterns (diffusers vs transformers module hierarchies), vectorized memory access strategies (BF16/FP16/FP32), and build matrix management via Nix flakes.

**Generated kernel projects** include:
- Vectorized CUDA kernels with warp shuffle reductions
- PyTorch C++ bindings for torch.compile registration
- Micro-benchmarks for validation
- build.toml configuration for kernel-builder

**Performance scaling**: Sequence length matters. RMSNorm speedup grows from 1.58x at 128 tokens to 2.47x at 8192 tokens on Qwen3-8B, showing long-context inference benefits.

**Practical limitation**: Single RMSNorm kernels contribute only 5–6% to end-to-end pipeline latency on compute-heavy models. Attention and projection kernels remain unoptimized, capping real-world gains. End-to-end speedup nearly doubles (1.06x → 1.43x) when combined with torch.compile, suggesting kernel generation is most effective as part of broader optimization.

## Implications

**For practitioners**: This is a viable pattern for delegating kernel optimization to agentic systems when bottlenecks are well-characterized (normalization, projections). Success depends on skill quality—architectural details must be precise. The approach reduces hiring pressure for kernel engineers if you can invest in high-quality skill libraries.

**Trade-off**: Agentic kernel generation works best for narrow, well-scoped kernels on known architectures. Attention kernels remain complex enough that this pattern has not yet scaled. Expect marginal gains on single kernels; batch optimizations with torch.compile matter more.

**Integration path**: Skills are reusable across tools (Claude Code, Cursor, Codex), making them valuable for teams with heterogeneous AI-assisted development workflows. Publishing to Hub with binary caching removes deployment friction—generated kernels become consumable as regular Python packages.

## Sources

- [Custom CUDA Kernels with Agent Skills - Hugging Face](https://huggingface.co/blog/custom-cuda-kernels-agent-skills)
- [We Got Claude to Build CUDA Kernels and teach open models](https://huggingface.co/blog/upskill)
- [Learn the Hugging Face Kernel Hub in 5 Minutes](https://huggingface.co/blog/hello-hf-kernels)
