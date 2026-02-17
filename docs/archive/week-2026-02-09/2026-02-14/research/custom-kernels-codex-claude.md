# Custom Kernels for All from Codex and Claude

| | |
|---|---|
| **Source** | Hugging Face Blog |
| **URL** | [huggingface.co/blog/custom-cuda-kernels-agent-skills](https://huggingface.co/blog/custom-cuda-kernels-agent-skills) |
| **Researched** | 2026-02-14 |

## Overview

Hugging Face built an agent skill framework that automates CUDA kernel generation for AI models using Claude and Codex. Rather than requiring expert GPU programmers to hand-write optimizations, developers prompt AI agents with high-level kernel requests. The framework generates production-ready code complete with bindings, tests, and benchmarks—then distributes them as pre-compiled binaries through the Kernel Hub with zero-compilation deployment.

## Key Points

- **Agentic CUDA generation**: AI agents synthesize optimized kernels for specific GPU architectures (H100, A100, T4) with correct PyTorch/transformers integration automatically included
- **Agent skills pattern**: Domain knowledge packaged as structured guidance (~550 tokens of core instructions + templates, guides, and reference implementations) that can be installed into Claude, Codex, Cursor, and other coding agents
- **Pre-compiled distribution**: `get_kernel()` auto-detects Python/PyTorch/CUDA versions and downloads matching binaries from Hub in seconds—eliminates "dependency matrix hell"
- **1.88–1.94x isolated speedups**: RMSNorm kernels achieve 2x isolation speedup on H100 (long-context inference where sequence length dominates latency), 1.06x end-to-end on video generation, 1.43x with torch.compile fusion

## Technical Details

### Agent Skill Structure
```
.claude/skills/cuda-kernels/
├── SKILL.md                    # Core instructions
├── references/
│   ├── h100-optimization-guide.md
│   ├── kernel-templates.md
│   └── troubleshooting.md
└── scripts/
    ├── transformers_injection_example.py
    └── ltx_kernel_injection_example.py  # Diffusers integration
```

### Deployment Pipeline
Agent-generated projects include build.toml and Nix derivations for multi-variant compilation. Users build once:
```bash
nix run .#build-and-copy -L  # Builds for all Python/CUDA/PyTorch combos
```
Then publish to Hub and integrate in one line:
```python
from kernels import get_kernel
rmsnorm = get_kernel("org/kernel-name")
```

### Performance Characteristics

**LTX-Video RMSNorm (H100 BFloat16)** - Shape [4×4096×3072]:
- Custom: 0.173ms | PyTorch: 0.393ms | Speedup: **2.26x**
- Bandwidth: 34.7% of H100 theoretical (3,350 GB/s)

**End-to-end**: 2.87s → 2.70s (kernels alone), 2.01s with torch.compile

Speedup scales with sequence length—ideal for long-context inference where norm operations dominate.

## Implications

**For practitioners:**

1. **Eliminates specialization bottleneck**: No longer need expert GPU engineers on staff to gain kernel optimizations; AI agents handle the synthesis, testing, and integration
2. **Portable across coding agents**: Skills work in Claude Code, Codex, Cursor—reduces lock-in and enables skill reuse across your team's tooling
3. **Focus on what matters**: 1.88–1.94x isolated speedups compound when fused with operator fusion (torch.compile), but most value comes from *having* kernels rather than hand-tuning—diminishing returns after basic opt passes
4. **Distribution solved**: Pre-compilation eliminates runtime build complexity; kernel compatibility handled automatically by get_kernel()
5. **Architecture matters**: H100/A100 optimizations differ significantly; agents must understand shared memory constraints, warp organization, and memory bandwidth profiles per architecture

**Trade-offs & decision points:**

- **Accuracy**: Generated kernels match PyTorch numerically but require benchmark validation; agents don't yet handle some edge cases (complex activations, custom dtypes)
- **Scope**: RMSNorm speedups are real, but attention kernels (flash-attention variants) remain harder to synthesize correctly without expert constraints
- **Adoption friction**: Requires team familiarity with Nix builds and Hub workflows; not zero-friction yet

**When to use**: Production inference on A100/H100 for sequence-heavy models (transformers at long context, video generation). For short-context or bandwidth-non-dominant ops, gains are marginal.

## Sources

- [Custom CUDA Kernels via Agent Skills](https://huggingface.co/blog/custom-cuda-kernels-agent-skills) - Primary article on agent-driven kernel generation
- [We Got Claude to Build CUDA Kernels and teach open models](https://huggingface.co/blog/upskill) - Upskill framework and agent skill export mechanics
- [Claude-Built CUDA Kernel Skills, Now Portable to Open Models](https://argbe.tech/blog/news/2026-01-28/huggingface-upskill-cuda-agent-skills) - Coverage of skill portability across agents
- [KernelBench: Can LLMs Write Efficient GPU Kernels?](https://scalingintelligence.stanford.edu/pubs/kernelbench.pdf) - Benchmark evaluation of LLM-generated kernel correctness and performance
