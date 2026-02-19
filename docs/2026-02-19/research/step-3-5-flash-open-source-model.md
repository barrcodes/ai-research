# Step 3.5 Flash - Open-Source Foundation Model

| | |
|---|---|
| **Source** | StepFun Blog |
| **URL** | [static.stepfun.com/blog/step-3.5-flash/](https://static.stepfun.com/blog/step-3.5-flash/) |
| **Researched** | 2026-02-19 |

## Overview

Step 3.5 Flash is a sparse Mixture-of-Experts foundation model with 196B parameters but only 11B active per token. The architecture combines hybrid attention patterns, multi-token prediction, and head-wise gating to deliver competitive frontier reasoning (AIME 2025: 97.3%) while maintaining local deployability and efficient inference (100-350 tokens/sec).

## Key Points

- **Sparse MoE Design**: 196B total parameters with 11B active per token creates favorable compute-to-capability ratio vs dense models. 3:1 sliding-window-to-full-attention ratio balances context windows (256K) against computational cost.

- **Multi-Token Prediction (MTP-3)**: Simultaneous prediction of multiple future tokens breaks serial decoding bottleneck, enabling parallel verification and faster generation speeds than single-token approaches.

- **Frontier Reasoning at Scale**: AIME 2025 achieves 97.3% (99.8% with Python integration), HMMT 2025 reaches 96.2%, and τ²-Bench agentic tasks hit 88.2%—competitive with much larger proprietary models.

- **Deployable Locally**: Runs on consumer hardware (Apple M4 Max, NVIDIA DGX Spark) without requiring data center scale, enabling privacy-preserving deployment and integration with development toolchains (OpenClaw, Claude Code).

## Technical Details

**Architecture Innovations:**
- Head-wise gated attention maintains numerical stability with minimal overhead
- Hybrid attention interleaves SWA and full attention to optimize memory-compute trade-offs
- 256K context window support with reduced computational requirements

**Performance Profile:**
- Reasoning: AIME 97.3%, HMMT 96.2%
- Coding: SWE-bench Verified 74.4%, Terminal-Bench 2.0 51.0%
- Agentic: τ²-Bench 88.2%, BrowseComp 69.0%
- Generation speed: 100–350 tokens/sec on contemporary hardware

**Model Efficiency:**
The sparse activation pattern delivers "intelligence density"—activating only 11B of 196B parameters per token significantly reduces training and inference costs compared to dense 355B+ parameter models achieving similar results.

## Implications

**For Architecture Decisions:**
Sparse MoE demonstrates that selective computation (not scale alone) drives frontier reasoning. Organizations can achieve competitive inference efficiency by adopting sparse activation over dense parameter scaling—critical for cost-sensitive deployments and edge/local scenarios.

**For Developers:**
Local deployability eliminates API dependencies for sensitive workloads. Integration with Python and development tools positions this as viable replacement for cloud-based models when latency, privacy, or cost become constraints. Agentic capabilities (88.2% τ²-Bench) suggest usable tool-use and planning without fine-tuning.

**Trade-offs:**
Sparse MoE adds complexity (expert routing, load balancing) vs dense models. Multi-token prediction improves throughput but may increase latency for first-token (serial dependency). Agentic scores lag reasoning benchmarks—not yet matching specialized reasoning-chain models, but sufficient for autonomous system orchestration.

**When to Use:**
- Local deployment requiring inference privacy
- Reasoning-heavy workloads with acceptable latency (100-350 tok/sec)
- Cost-constrained inference at scale
- Integration with development workflows needing low-friction model swapping

## Sources

- [StepFun Blog: Step 3.5 Flash](https://static.stepfun.com/blog/step-3.5-flash/) - Official model announcement with technical specifications and benchmark results
