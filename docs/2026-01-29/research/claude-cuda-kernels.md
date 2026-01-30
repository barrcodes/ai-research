# We Got Claude to Build CUDA Kernels and teach open models!

| | |
|---|---|
| **Source** | Hugging Face Blog |
| **URL** | [huggingface.co/blog/upskill](https://huggingface.co/blog/upskill) |
| **Researched** | 2026-01-29 |

## Overview

Hugging Face demonstrates using Claude Opus 4.5 to generate reusable "Agent Skills"—structured knowledge packages that teach smaller and open-source models to build optimized CUDA kernels. The approach treats skilled instruction as a one-time investment by expensive models, enabling cost-effective deployment across cheaper models with measurable accuracy improvements.

## Key Points

- **Skills as structured knowledge**: Domain expertise captured in ~500-token markdown files with embedded architecture specifics, conventions, and test cases—automating what would take hours to gather from documentation
- **Dramatic accuracy gains for smaller models**: Sonnet improves 60%→95%, Unsloth GLM-4.7-Flash jumps 40%→85%; Claude Opus 4.5 maintains baseline (already capable)
- **Automated evaluation pipeline**: `upskill` tool generates test cases, runs baseline vs. skill-enhanced comparisons across model families, and identifies cost-performance sweet spots
- **Cross-tool portability**: Skills follow Agent Skills specification—work with Claude Code, Cursor, local models via OpenAI-compatible endpoints, enabling standardized knowledge distribution
- **Selective deployment**: Skills don't universally improve performance; evaluation prevents degradation (e.g., unnecessary token overhead for already-capable models)

## Technical Details

**Skill structure** encodes H100-specific optimizations:
- Compute capability 9.0 targeting (not 8.0)
- 128-byte shared memory alignment
- `__CUDA_ARCH__ >= 900` async operations
- `build.toml` project conventions

**Generation and evaluation**:
```bash
upskill generate "build optimized CUDA kernels for PyTorch"
upskill eval ./skills/kernel-builder/ --model haiku --model sonnet --runs 5
```

Auto-generated test cases verify knowledge transfer:
```json
{"input": "Create build.toml for H100", "expected": {"contains": "9.0"}}
```

**Performance matrix**: Skills create compatibility matrices showing which model-task combinations benefit—critical for cost optimization decisions.

## Implications

**Cost multiplication**: Generate skills once with expensive models; deploy across cheap models. Example: Replace Opus calls with Haiku + skill where 85%+ accuracy suffices.

**Democratization**: Complex domains (CUDA optimization, internal tooling) become accessible to 7B parameter models when knowledge is captured structurally rather than contextually.

**Measurable quality gates**: Built-in evaluation prevents silent degradation and quantifies improvements per model, enabling confident model selection for specific workloads.

**Generalization**: Pattern extends beyond CUDA to YAML parsing, codebase conventions, proprietary frameworks—any domain where human expertise varies across model capabilities.

## Related

- [Agent Skills specification](https://github.com/anthropics/claude-code) - Cross-tool standard for knowledge packaging
- Claude Opus 4.5 - Baseline model for skill generation
- Unsloth GGUF models - Cost-efficient inference platforms
