# We Got Claude to Teach Open Models How to Write CUDA Kernels

| | |
|---|---|
| **Source** | Hugging Face Blog |
| **URL** | [huggingface.co/blog/upskill](https://huggingface.co/blog/upskill) |
| **Researched** | 2026-02-02 |

## Overview

Hugging Face introduced **upskill**, a framework that extracts Claude Opus 4.5's reasoning patterns into portable **agent skills**—reusable markdown instruction sets that transfer specialized expertise to smaller or open-source models. The approach achieves 35-45% accuracy gains on complex tasks like CUDA kernel writing while reducing per-task costs by deploying to cheaper inference engines.

## Key Points

- **Skill Generation**: Claude Opus solves a specialized problem (e.g., CUDA kernel development), and the agent trace is compiled into a ~500-token SKILL.md file capturing domain knowledge, project templates, and validation logic.

- **Cost Arbitrage**: Generate expensive expertise once with Opus, then deploy to local models or cheaper APIs (GLM-4.7-Flash, Haiku) with minimal accuracy degradation. Critical decision point: evaluate token efficiency, not just accuracy—some models consume more tokens with skills despite same accuracy.

- **Model-Agnostic Portability**: Skills follow the Agent Skills specification, working across Claude Code, Cursor, and other agentic tools. No vendor lock-in to a single inference provider.

- **Selective Wins**: Performance gains vary dramatically by task and model combination. Kimi-K2-Thinking showed strong improvements on both accuracy and token efficiency; Opus showed accuracy gains but increased token consumption (not recommended for cost optimization).

## Technical Details

The workflow is straightforward:

1. **Teacher generates**: Claude Opus interactively builds the solution with Claude Code, creating a documented trace
2. **Extract skill**: `upskill generate "your task"` or `upskill generate "your task" --from <trace.md>`
3. **Deploy & measure**: `upskill eval ./skills/my-skill/ --model haiku --base-url http://localhost:8080/v1`

Evaluation methodology uses structured test cases with expected outputs:

```json
{
  "cases": [
    {
      "input": "Create a build.toml for CUDA targeting H100",
      "expected": {"contains": "9.0"}
    }
  ]
}
```

Results on CUDA kernel writing: Opus +35% (60% → 95%), GLM-4.7-Flash +45% (40% → 85%).

## Implications

This pattern reframes expensive frontier models as **knowledge distillers** rather than production workhorses. For organizations with repetitive specialized tasks, the ROI is clear: invest once in deep reasoning, extract repeatable procedures, deploy at scale.

**Architecture trade-offs**:
- *Pro*: Significant cost reduction, portable across tools, captures organizational knowledge
- *Con*: Skills require domain expertise to generate and validate; some models show worse token efficiency despite accuracy gains; no guarantee of transfer across model classes

**When to use**: Domain-specific workflows with high repetition, internal tools, codegen tasks, knowledge capture. Skip for one-off problems or when the student model already handles the task efficiently.

**Alternative**: Fine-tuning achieves similar gains but requires labeled datasets and retraining infrastructure. Skills are lower-friction for knowledge transfer.

## Sources

- [Hugging Face Blog: Upskill](https://huggingface.co/blog/upskill) - Framework and methodology for extracting and deploying agent skills
- [Agent Skills Specification](https://agentskills.io) - Standard for portable skill definitions
