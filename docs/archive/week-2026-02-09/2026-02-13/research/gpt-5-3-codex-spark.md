# Introducing GPT-5.3-Codex-Spark

| | |
|---|---|
| **Source** | OpenAI |
| **URL** | [openai.com/index/introducing-gpt-5-3-codex-spark](https://openai.com/index/introducing-gpt-5-3-codex-spark/) |
| **Researched** | 2026-02-13 |

## Overview

OpenAI released GPT-5.3-Codex-Spark, a real-time coding model optimized for sub-second latency by running on Cerebras' Wafer Scale Engine 3. This marks the first major milestone in OpenAI's $10B+ partnership with Cerebras and represents a strategic shift toward custom silicon for inference optimization. Unlike previous Codex versions focused on capability, Spark prioritizes responsiveness for iterative development workflows.

## Key Points

- **Throughput**: Delivers 1,000+ tokens/second, enabling near-instant feedback in live coding environments
- **Hardware**: Runs on Cerebras WSE-3 (non-NVIDIA), marking OpenAI's first production deployment on alternative silicon
- **Model Size**: Smaller, specialized version of GPT-5.3-Codex, trading off raw capability for inference speed
- **Capability Retention**: Outperforms GPT-5.1-Codex-mini on SWE-Bench Pro and Terminal-Bench 2.0 while completing tasks in a fraction of the time
- **Context Window**: 128K tokens (text-only for now; multimodal planned)
- **Availability**: Research preview to ChatGPT Pro users; limited API access for design partners

## Technical Details

Codex-Spark achieves its latency improvements through joint optimization of model architecture and specialized hardware. The Wafer Scale Engine provides "the largest on-chip memory of any AI processor," enabling high-speed inference without the latency penalties of distributed systems.

The model's design focuses on real-time developer interactions: precise code edits, plan revisions, and contextual codebase questions. On agentic benchmarks (SWE-Bench Pro, Terminal-Bench 2.0), it produces results comparable to heavier models while dramatically reducing inference time.

**Current Constraints**: Limited to text input and 128K context. Vision and longer contexts are roadmapped for future releases.

## Implications

This release signals a maturation in the coding AI market. Rather than pursuing raw capability, OpenAI is optimizing for developer experience—the latency/capability tradeoff now favors interactivity. For practitioners, this means:

**Architectural Impact**: Custom silicon for inference is becoming strategic parity, not differentiator. The Cerebras partnership suggests vertically integrated infrastructure is necessary for competitive latency in real-time AI applications.

**When to Use**: Codex-Spark targets synchronous coding workflows (IDE integration, pair programming, real-time refactoring). Previous Codex versions remain better for async batch tasks requiring maximal capability.

**Market Signal**: The $10B Cerebras deal indicates OpenAI expects long-term value from alternative chip vendors, reducing dependency on NVIDIA and hedging against supply constraints.

**Developer Friction**: Sub-second feedback could shift how teams interact with AI—from "query and wait" to truly "conversational" coding. This lowers adoption barriers in IDEs where latency currently limits integration.

## Sources

- [Introducing GPT-5.3-Codex-Spark](https://openai.com/index/introducing-gpt-5-3-codex-spark/) - Official OpenAI announcement
- [Introducing OpenAI GPT-5.3-Codex-Spark Powered by Cerebras](https://www.cerebras.ai/blog/openai-codexspark) - Cerebras technical details
- [OpenAI's new Codex Spark model is built for speed](https://thenewstack.io/openais-new-codex-spark-is-optimized-for-speed/) - Infrastructure analysis
- [A new version of OpenAI's Codex is powered by a new dedicated chip](https://techcrunch.com/2026/02/12/a-new-version-of-openais-codex-is-powered-by-a-new-dedicated-chip/) - Hardware partnership context
- [GPT-5.3-Codex-Spark: OpenAI's 1000 tok/s Real-Time Coding Model](https://www.nxcode.io/resources/news/gpt-5-3-codex-spark-real-time-coding-guide-2026) - Technical specifications
