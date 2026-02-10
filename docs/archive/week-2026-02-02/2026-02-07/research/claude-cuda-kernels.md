# We Got Claude to Build CUDA Kernels and Teach Open Models

| | |
|---|---|
| **Source** | Hugging Face Blog |
| **URL** | [huggingface.co/blog/upskill](https://huggingface.co/blog/upskill) |
| **Researched** | 2026-02-07 |

## Overview

Hugging Face demonstrated a method for transferring GPU optimization expertise from Claude Opus 4.5 to smaller/open-source models via "agent skills"—reusable knowledge artifacts (~520 tokens) extracted from successful CUDA kernel implementations. Open models improved accuracy by +35-45% when using the skill, enabling cost-effective deployment of specialized GPU knowledge.

## Key Points

- **Three-stage pipeline**: Claude generates CUDA kernels interactively → `upskill` tool extracts distilled knowledge → skills transfer to cheaper models
- **Significant accuracy lift for open models**: GLM-4.7-Flash jumped from 40% → 85% accuracy with the CUDA kernel-builder skill; Moonshotai/Kimi-K2-Thinking showed similar gains with reduced token overhead
- **Hardware-specific knowledge encoded**: Skills capture H100-targeting patterns (compute capability 9.0), shared memory alignment constraints, build configuration templates, and async copy requirements—encapsulating domain expertise that non-specialists couldn't otherwise replicate
- **Smaller models achieve better cost efficiency**: While Opus 4.5 showed no accuracy benefit and increased token usage with the skill, smaller models combined accuracy gains with lower per-token cost
- **Cross-tool portability**: Skills follow the Agent Skills specification, deployable across Claude Code, Cursor, and other integrations

## Technical Details

### Skill Architecture
Skills are knowledge packets containing:
- SKILL.md: Distilled instructions (~520 tokens) with H100 optimization patterns, PyTorch binding examples, and project structure templates
- skill_meta.json: Auto-generated test cases validating understanding (e.g., "generate build.toml for H100" → must contain "9.0" capability flag)

### Build Configuration Template
```toml
[general]
name = "kernel_project"
backends = ["cuda"]

[general.cuda]
capabilities = ["9.0"]  # H100 compute capability
```

### Asymmetric Performance Profile
| Model | Baseline | With Skill | Token Cost | Recommendation |
|-------|----------|-----------|------------|---|
| Claude Opus 4.5 | 60% | 95% | ↑ (undesirable) | Embed knowledge directly |
| GLM-4.7-Flash | 40% | 85% | → ↓ | Use skill |
| Moonshotai/Kimi-K2 | — | Significant lift | ↓ | Use skill |

## Implications

**Capability Redistribution Model**: This inverts traditional scaling economics. Rather than paying for larger models, organizations can now:
1. Use Opus 4.5 once to generate/refine skills for specific domains (CUDA, ML frameworks, infrastructure patterns)
2. Deploy those skills to Haiku-class models or open alternatives for 80-90% of the accuracy at <20% the cost

**Practical Impact for GPU Infrastructure**:
- **Non-specialists can generate production CUDA code** if equipped with a domain skill
- **Iterative skill improvement**: Failure cases feed back into `upskill generate --from {skill}` to continuously refine knowledge artifacts
- **Hardware evolution path**: New GPU families (Blackwell, next-gen) would require skill regeneration rather than expensive model retraining

**When Not to Use Skills**: Generalist tasks where smaller models already perform well see no benefit and incur token overhead. Skills target high-skill-gap domains.

**Architectural Significance**: This demonstrates practical knowledge distillation beyond simple fine-tuning—capturing *procedural expertise* (how to think about CUDA constraints) rather than just task-specific patterns.

## Sources
- [Hugging Face Blog: We Got Claude to Build CUDA Kernels](https://huggingface.co/blog/upskill) - Full article with benchmarks and methodology
