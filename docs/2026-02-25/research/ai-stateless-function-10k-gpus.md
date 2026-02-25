# AI: A Stateless Function on 10k GPUs Pretending to Know You

| | |
|---|---|
| **Source** | YouTube |
| **URL** | [youtube.com/watch?v=3cYS9uukxaY](https://www.youtube.com/watch?v=3cYS9uukxaY) |
| **Researched** | 2026-02-25 |

## Overview

This talk examines stateless GPU function architecture as a pattern for deploying AI inference and batch workloads across large-scale GPU clusters (10,000+ GPUs). The stateless design enables elastic scaling, cost efficiency, and simplified operational complexity by eliminating persistent state requirements and embracing serverless elasticity principles adapted to GPU compute environments.

## Key Points

- **Stateless Functions as Scaling Pattern**: Each GPU function executes independently with zero inter-request dependencies. This enables elastic scaling from zero to thousands of GPUs based on demand, with autoscaling decisions decoupled from function logic.

- **Sub-Second Cold Starts**: Modern serverless GPU platforms (Modal, RunPod) achieve GPU container provisioning in <1 second, reducing cold-start overhead to negligible levels when amortized across batch workloads. 3-5 second GPU cold starts remain acceptable for enterprise workloads when amortized to <5% of request latency through keep-warm strategies.

- **Scale-to-Zero Economics**: Per-second billing models eliminate idle GPU infrastructure costs. Cluster scales to zero when traffic ceases, recovering GPUs for other workloads. Enterprise deployments demonstrate 10x cost reduction versus dedicated GPU clusters through elasticity.

- **Multi-Cloud Capacity Pooling**: Stateless functions decouple workloads from specific GPU hardware (H100, A100, L40S). Intelligent scheduling routes jobs to available capacity across cloud providers, enabling arbitrage and disaster recovery without application changes.

## Technical Details

Stateless GPU function architecture operates on these principles:

**Container Model**: Each function request maps to an isolated container with its own Python runtime, model weights, and inference context. No shared memory across invocations eliminates concurrency complexity.

**Scaling Characteristics**:
- **Horizontal elasticity**: 1,000+ concurrent functions spawn in <10 seconds
- **Batch amortization**: Kernel launch overhead decreases from 10-20% at batch size 1 to <1% at batch size 1,000
- **P99 latency at scale**: Maintained at <20ms even with elastic scaling overhead through batching strategies

**Performance Trade-offs**:
- Cold start: 3-5 seconds GPU vs. 0.5-1 second CPU
- Throughput: GPU functions achieve 4,565-5,200 samples/second; CPU clusters 877-1,200 samples/second at equivalent batch sizes
- Compliance overhead: FIPS/IL4 encryption adds 5.7ms latency while preserving <20ms P99 tail latency

**Memory Constraints**: GPU serverless fits datasets ≤20GB (typical GPU VRAM: 16-24GB). Spillover to disaggregated storage (MinIO, encrypted AES-256) adds latency; model weights + activations consume bulk capacity.

## Implications

**For LLM Inference**: Stateless GPU functions enable cost-effective serving of fine-tuned models without maintaining warm GPU pools. Pre-warming selected high-traffic functions to <5% idle cost overhead balances latency and expense.

**For Batch Training**: Disaggregated GPU workers scale training jobs horizontally without distributed state management (NCCL, gradient synchronization complications). Training batches launch as independent functions, reducing operational complexity at cost of inter-node coordination overhead.

**Architecture Decision**: Adopt stateless GPU functions when:
- Workloads have bursty or variable traffic patterns
- Cost optimization outweighs latency sensitivity (batch inference, report generation, fine-tuning)
- Team lacks distributed systems expertise (vs. self-managed GPU clusters)

**Avoid when**:
- Real-time serving requires <100ms P99 latency (cold start dominates)
- Workloads maintain persistent gradient state across mini-batches
- Total GPU demand is <100 GPUs (fixed cluster cost-optimal)

**Multi-Cloud Arbitrage**: Deploy stateless functions across regions/clouds. Pricing fluctuates hourly; schedule batch jobs when spot GPU costs drop (e.g., B100s at $0.40/hr vs. $1.20 peak). Stateless design makes switching cloud providers trivial.

## Sources

- [Modal: High-performance AI infrastructure](https://modal.com/) - Serverless GPU platform implementing stateless function scaling
- [Serverless GPU Architecture for Enterprise HR Analytics: A Production-Scale BDaaS Implementation](https://arxiv.org/html/2510.19689v1) - Benchmarks and resource management patterns
- [RunPod Serverless GPUs](https://www.runpod.io/product/serverless) - Alternative stateless GPU deployment platform
- [DGSF: Disaggregated GPUs for Serverless Functions](https://par.nsf.gov/servlets/purl/10340800) - Research on disaggregated GPU scheduling
- [Towards Fast Setup and High Throughput of GPU Serverless Computing](https://arxiv.org/abs/2404.14691) - Academic research on GPU serverless optimization
