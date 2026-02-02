# Introducing Claude Haiku 4.5

| | |
|---|---|
| **Source** | Anthropic |
| **URL** | [anthropic.com/news/claude-haiku-4-5](https://www.anthropic.com/news/claude-haiku-4-5) |
| **Researched** | 2026-01-26 |

## Overview

Anthropic released Claude Haiku 4.5, a lightweight model matching Sonnet 4's coding performance while delivering 2-4.5x faster throughput at one-third the cost. The model targets latency-sensitive production workloads, developer tools, and agentic orchestration patterns where cost-per-inference matters more than frontier reasoning capability.

## Key Points

- **Performance parity**: Achieves similar coding performance to Claude Sonnet 4 (five-month-old baseline), with superiority on specific tasks like computer use and vision-based reasoning
- **Speed/cost advantage**: 2x faster than Sonnet 4; 4-5x faster than Sonnet 4.5 at significantly lower token costs ($1/$5 per million input/output tokens vs. Sonnet's $3/$15)
- **Expanded safety profile**: ASL-2 classification (vs. Sonnet's ASL-3) allows broader deployment scenarios with lower compliance friction
- **Multimodal ready**: Handles text, image, and video inputs alongside standard code/tool use
- **Availability**: Deployed across Anthropic's API, Amazon Bedrock, and Google Cloud Vertex AI

## Technical Details

| Metric | Haiku 4.5 | Sonnet 4 | Sonnet 4.5 |
|--------|-----------|---------|-----------|
| Throughput | Baseline | ~2x slower | 4-5x slower |
| Cost/1M tokens | $1/$5 | $3/$15 | ~$5/$15+ |
| Coding performance | Equivalent | Reference | Superior |
| Safety level | ASL-2 | ASL-3 | ASL-3 |

Haiku 4.5 shines on computer use tasks (visual reasoning + action generation) and handles vision processing effectively, making it viable for screenshot-based automation and multimodal applications.

## Implications

**When to use Haiku 4.5:**
- Customer service bots, chat assistants requiring sub-500ms latency
- Pair programming and code completion at reasonable performance
- Agentic patterns: Deploy Haiku 4.5 agents orchestrated by Sonnet 4.5 for cost-efficient horizontal scaling
- Vision automation tasks and computer use workflows
- Cost-constrained production deployments where latency tolerance exists

**When to stick with Sonnet 4.5:**
- Complex reasoning, research-grade tasks, novel problem-solving
- Where response quality outweighs latency (batch processing, analysis)
- Frontier reasoning on untested domains

**Architecture shift**: The Haiku/Sonnet cost ratio (1:5 for input) enables new patterns—orchestrate one Sonnet coordinator managing dozens of parallel Haiku subtasks. This trades some reasoning capability per task for throughput and cost efficiency.

## Related

- [Claude models comparison](https://www.anthropic.com/models) - Full capability matrix across all models
- [Bedrock pricing](https://aws.amazon.com/bedrock/pricing/) - Real-time cost estimates on AWS
