# SHELLper: 0.6B Model Excels at Multi-Turn Function Calling

| | |
|---|---|
| **Source** | Distil Labs (GitHub) |
| **URL** | [github.com/distil-labs/distil-SHELLper](https://github.com/distil-labs/distil-SHELLper) |
| **Researched** | 2026-01-26 |

## Overview
SHELLper demonstrates that aggressive model compression—fine-tuning Qwen3 0.6B for bash command generation—achieves near-perfect accuracy (100%) on multi-turn function calling tasks. The model converts natural language to executable bash commands through specialized training on synthetic data, proving that function calling doesn't require massive frontier models when properly optimized.

## Key Points
- **600M parameters**: Achieves 100% test accuracy on 5-turn bash command chains, compared to 42.22% for base Qwen3 0.6B
- **Synthetic training pipeline**: Three-phase approach (20 seed examples → synthetic expansion → fine-tuning) scales training data without manual annotation
- **Multi-turn chaining**: Handles sequential tool calls while managing compounding error—critical for real workflows where single mistakes cascade
- **Local deployment**: Enables privacy-preserving bash automation without cloud API calls or proprietary models
- **Constrained function space**: Limited to core filesystem operations (ls, cd, mkdir, rm) with no pipes or combined commands—intentional design reducing hallucination surface area

## Technical Details

### Model Architecture
- **Base**: Qwen3 0.6B language model
- **Target**: Multi-turn tool calling for bash command generation
- **Training constraint**: Maximum 5 conversation turns to keep learning problem tractable

### Performance Improvements
| Metric | Base Qwen3 0.6B | Fine-tuned SHELLper | Teacher (Qwen3 235B) |
|--------|-----------------|-------------------|----------------------|
| Single-turn accuracy | 84.16% | 100% | 99% |
| Five-turn accuracy | 42.22% | 100% | 95% |

The gap between 84% single-turn and 42% five-turn in base model reveals the compounding error problem—each wrong command reduces subsequent context quality. Specialized fine-tuning eliminates this entirely.

### Training Data Generation
- **Seeds**: 20 manually curated conversation examples
- **Scale**: Synthetic expansion via Distil Labs' data synthesis platform
- **Output**: Thousands of training examples from minimal manual work

## Implications

**For practitioners building tool-calling systems**: This work proves that aggressive model distillation works when applied to narrow, well-defined domains. Rather than relying on 70B+ parameter models, define your function space tightly, generate synthetic training data, and fine-tune efficiently.

**Trade-offs**: SHELLper's constraint to basic filesystem operations isn't a limitation—it's a feature. By reducing hallucination surface area (no pipes, no command chaining), you get reliable automation instead of spectacular failures.

**When to apply this approach**:
- Private, on-device automation (sensitive operations, no cloud dependency)
- Cost-sensitive deployments at scale
- Custom workflows where you control the function schema

**Alternatives**: Larger models (70B+) offer broader flexibility but cost significantly more in inference, latency, and memory. Prompt engineering alone on base models hits hard ceilings on multi-turn accuracy.

## Related
- [Distil Labs GitHub](https://github.com/distil-labs/distil-SHELLper) - Model and training code
- Qwen3 0.6B models - Foundational architecture
- Tool use benchmarks (MARBLE, API-Bank) - Related multi-turn function calling evaluation sets
