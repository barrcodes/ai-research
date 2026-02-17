# Fine-Tuned FunctionGemma 270M: Achieving 90-97% Multi-Turn Tool Calling Accuracy

| | |
|---|---|
| **Source** | Distil Labs |
| **URL** | [distillabs.ai/blog/making-functiongemma-work-multi-turn-tool-calling-at-270m-parameters](https://www.distillabs.ai/blog/making-functiongemma-work-multi-turn-tool-calling-at-270m-parameters) |
| **Researched** | 2026-02-17 |

## Overview

FunctionGemma 270M ships untrained for multi-turn tool calling, scoring 10-39% accuracy. Through targeted fine-tuning on three production domains using the Distil Labs platform, accuracy improved to 90-97%, matching or exceeding a 120B teacher model while maintaining a 445× size advantage (270M vs. 120B parameters). The model remains deployable on CPU with 125 tokens/sec throughput and ~288MB quantized footprint.

## Key Points

- **Baseline problem**: Base FunctionGemma is explicitly intended for fine-tuning; out-of-the-box multi-turn accuracy is 9.9-38.8% across three tasks, rendering it unusable for production
- **Multi-turn compounding**: Single-turn accuracy directly compounds across conversation turns (80% single-turn = 33% for 5-turn conversations); every percentage point becomes critical
- **Fine-tuning results**: Three domains show consistent 50+ percentage point improvements:
  - Smart home control: 38.8% → 96.7%
  - Banking voice assistant: 23.4% → 90.9%
  - Shell command execution: 9.9% → 96.0%
- **Knowledge distillation method**: Used GPT-4o-120B as teacher to generate synthetic multi-turn examples, validated and filtered, then fine-tuned FunctionGemma independently on task-specific datasets
- **Data quality is decisive**: Same high-quality dataset that works for Qwen3-0.6B produces strong FunctionGemma results without model-specific tuning

## Technical Details

**Evaluation Methodology:**
- Primary metric: Tool Call Equivalence (exact match of predicted vs. reference as Python dict)
- Secondary: ROUGE for surface-level similarity (less informative for structured outputs)
- Critical insight: Binary pass/fail on structured outputs—partial credit metrics mask real-world production failures

**Task Complexity:**
- Smart home: Constrained function catalog, consistent patterns, strongest results (96.71%, exceeds 120B teacher at 92.11%)
- Banking: 14 functions with complex slots, ASR transcription noise, intent changes mid-conversation (90.86% vs. teacher 96.95%—gap attributed to larger catalog + ASR artifacts)
- Shell commands: Gorilla benchmark, minimal context, smallest training set (96.04%, matches teacher at 97.03%)

**Architectural Advantage:**
- 270M parameters fit in ~288MB quantized GGUF
- Runs at 125 tokens/sec on CPU cores
- Latency 10-50ms on single CPU core
- Enables on-device inference: mobile, browser-based, embedded systems

## Implications

1. **FunctionGemma as production baseline**: For any latency-critical or resource-constrained agentic system, start here rather than larger models. The 445× size reduction is architectural, not a compromise on performance when properly fine-tuned.

2. **Task-specific fine-tuning is non-negotiable**: The base model's poor performance (10-39%) is expected and documented. Organizations deploying FunctionGemma must plan for fine-tuning effort. No off-the-shelf solution exists for multi-turn cases.

3. **Data quality over architectural innovation**: The same datasets that work for other small models work for FunctionGemma. Effort should focus on data curation and validation (filtering synthetic examples from teacher) rather than novel modeling approaches.

4. **Production orchestration required**: Even at 90%+ accuracy, edge cases remain (especially in complex domains like banking with ASR noise). Deploy with fallback slot-elicitation loops or human escalation for confidence thresholds below expected quality.

5. **Tool calling vs. open-ended generation**: This is a solved problem for structured extraction. The extreme compounding of errors across turns means multi-turn tool calling is fundamentally different from open-ended language generation—treat it as a classification/extraction task, not text generation.

6. **Edge deployment enablement**: The combination of size and performance enables new architectures previously impossible: on-device voice assistants, browser-based function calling, offline-first mobile agents. This shifts where agentic bottlenecks lie (network I/O, not inference).

## Sources

- [Making FunctionGemma Work: Multi-Turn Tool Calling at 270M Parameters](https://www.distillabs.ai/blog/making-functiongemma-work-multi-turn-tool-calling-at-270m-parameters) - Distil Labs technical report with three task benchmarks
- [FunctionGemma model card](https://huggingface.co/google/functiongemma-270m-it) - Google's official model documentation
- [Distil Labs fine-tuned models](https://huggingface.co/distil-labs) - Production-ready checkpoints for smart home, banking, and shell command tasks
