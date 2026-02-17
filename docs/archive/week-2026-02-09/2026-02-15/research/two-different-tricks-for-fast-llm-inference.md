# Two Different Tricks for Fast LLM Inference

| | |
|---|---|
| **Source** | Sean Goedecke |
| **URL** | [seangoedecke.com/fast-llm-inference/](https://www.seangoedecke.com/fast-llm-inference/) |
| **Researched** | 2026-02-15 |

## Overview

The article contrasts two fundamentally different approaches to accelerating LLM inference: Anthropic's low-batch-size strategy that prioritizes latency and model fidelity, and OpenAI's specialized hardware with model distillation that prioritizes throughput. These represent distinct architectural trade-offs: latency vs. throughput, cost vs. speed, and full capability vs. reduced quality.

## Key Points

- **Low-batch-size inference** reduces batch sizes on existing infrastructure to serve requests immediately, yielding 2.5x token throughput improvement but at 6x cost overhead
- **Specialized hardware with distillation** uses Cerebras chips (44GB internal SRAM, 70 sq. in.) to fit entire models in fast memory, achieving 15x throughput but with distilled, lower-quality models
- The approaches represent opposing optimization axes: Anthropic trades cost for latency + accuracy; OpenAI trades capability for speed
- Anthropic's method delivers the "actual model" (real Opus 4.6) while OpenAI uses a "Spark" distilled variant that exhibits more errors and tool-call confusion

## Technical Details

**Anthropic's Approach:**
- Eliminates batch wait times by processing individual requests immediately
- Performance: 170 tokens/sec vs. baseline 65 tokens/sec
- Cost multiplier: 6x (unused batch capacity)
- Suitable for CPU/GPU batching inefficiency scenarios

**OpenAI's Approach:**
- Cerebras chips provide 44GB low-latency SRAM (on-die, not external)
- Model distillation reduces complexity to fit within chip memory constraints
- Performance: 1000+ tokens/sec (15x improvement)
- Trade-off: Distilled "Spark" model introduces capability gaps

## Implications

The choice between these approaches depends on your optimization goal:

| Dimension | Anthropic | OpenAI |
|-----------|-----------|--------|
| Latency | 2.5x faster | 15x faster |
| Cost | 6x overhead | High hardware capital |
| Model quality | Full capability | Reduced (distilled) |
| Best for | High-accuracy tasks, interactive apps | Throughput-critical, fault-tolerant workloads |

For practitioners: If you're building systems where model accuracy is non-negotiable (coding assistants, complex reasoning), Anthropic's approach justifies the cost. If you're building high-throughput primitives within larger systems (RAG chunking, pre-filtering), OpenAI's hardware-backed approach wins. The critical insight is that raw "speed" metrics (tokens/sec) obscure the real trade-off—paying for accuracy and latency vs. sacrificing both for throughput.

## Sources

- [seangoedecke.com/fast-llm-inference/](https://www.seangoedecke.com/fast-llm-inference/) - Original analysis comparing Anthropic and OpenAI inference strategies
