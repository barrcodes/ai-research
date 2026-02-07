# Benchmarking Total Wait Time Instead of pp/tg

| | |
|---|---|
| **Source** | Reddit Discussion |
| **URL** | [i.redd.it/dmf3ykavv3ig1.png](https://i.redd.it/dmf3ykavv3ig1.png) |
| **Researched** | 2026-02-07 |

## Overview

The discussion challenges the common practice of reporting isolated prefill and token-generation metrics (pp/tg) as primary LLM benchmarking measures. Instead, it advocates for end-to-end total wait time—the actual latency users experience—as the more architecturally significant metric that better captures real-world system performance and reveals the throughput-latency trade-offs obscured by granular token-rate metrics.

## Key Points

- **Metric Blindness**: pp/tg metrics (prefill per token, token generation rates) can mask poor real-world performance. A system achieving high tokens-per-second on paper may still subject users to unacceptable queuing delays when concurrent requests arrive.

- **User Experience vs. Theoretical Throughput**: End-to-end latency directly correlates with perceived responsiveness and SLO compliance. Total wait time captures both compute time and queueing delays, whereas isolated token-rate metrics ignore request batching effects and scheduling under load.

- **Batching Dynamics**: When concurrency exceeds maximum batch size, requests queue for GPU slots. pp/tg metrics remain constant, but user-facing latency degrades significantly. This explains why high TPS numbers often coexist with poor interactive experience.

- **Architectural Decision Point**: Choosing between optimizing for TTFT (Time to First Token) versus throughput involves inherent trade-offs. Prefill-optimized batching improves inter-token latency but increases overall request completion time; decode-optimized scheduling boosts TPS while degrading responsiveness.

## Technical Details

Standard LLM benchmarking decomposes performance as:

**End-to-End Latency = TTFT + (Tokens Generated × ITL) + Queueing Delay**

Where:
- **TTFT** (Time to First Token): Prompt processing + prefill compute
- **ITL** (Inter-Token Latency): Per-token decode time
- **Queueing Delay**: Wait before GPU slot availability

The critical insight: isolated pp/tg metrics report only the first two components. Total wait time includes the third, which dominates under load.

**Prefill-Heavy vs. Decode-Heavy Workloads**:
- High pp (fast prefill): Suits long-context tasks, improves TTFT, but may starve concurrent requests
- High tg (fast generation): Maximizes throughput but increases time-between-tokens for streaming applications

**Goodput**: Beyond raw throughput, "goodput" measures requests meeting latency SLOs. A system with 1000 RPS is valueless if 80% violate tail latency targets.

## Implications

**For Infrastructure Teams**: Stop relying on pp/tg benchmarks as deployment criteria. Measure end-to-end latency under production-like concurrency levels. Establish SLO targets (e.g., P50 <100ms, P99 <2s) and validate achievability before scaling.

**For Model Selection**: Token rate improvements mean little if batch scheduling creates queue delays. Prefer models/serving approaches with predictable tail latency over those with higher peak throughput.

**For Cost-Performance Trade-offs**: Batching depth affects both metrics. Deeper batches increase system TPS but degrade per-request latency. Total wait time forces honest accounting of this trade-off rather than cherry-picking favorable metrics.

**Benchmarking Best Practice**: Report total wait time at realistic concurrency levels (ideally from production traces) rather than synthetic single-request scenarios. Include latency percentiles (P50, P95, P99) and queueing metrics. This shifts focus from theoretical optimization toward user-centric performance.

## Sources

- [Understand LLM latency and throughput metrics | Anyscale Docs](https://docs.anyscale.com/llm/serving/benchmarking/metrics) - Detailed explanation of TTFT, ITL, TPS metrics and latency-throughput trade-offs
- [LLM Inference Benchmarking: Fundamental Concepts | NVIDIA Technical Blog](https://developer.nvidia.com/blog/llm-benchmarking-fundamental-concepts/) - Core benchmarking methodology and sequence length impact
- [Metrics — NVIDIA NIM LLMs Benchmarking](https://docs.nvidia.com/nim/benchmarking/llm/latest/metrics.html) - Standardized metric definitions for LLM serving
- [Throughput-Latency tradeoff in LLM Inference | Medium](https://medium.com/better-ml/throughput-latency-tradeoff-in-llm-inference-5a9e0d1d2c14) - Analysis of scheduling trade-offs in LLM serving
