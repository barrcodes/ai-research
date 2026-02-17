# Step-3.5-Flash Model - Features and Performance

| | |
|---|---|
| **Source** | StepFun Official Blog |
| **URL** | [static.stepfun.com/blog/step-3.5-flash/](https://static.stepfun.com/blog/step-3.5-flash/) |
| **Researched** | 2026-02-09 |

## Overview

StepFun's Step-3.5-Flash achieves frontier-level reasoning on a sparse 196B parameter model by activating only 11B per token, delivering 100-350 tok/s throughput while outperforming much larger models (DeepSeek V3.2, Kimi K2.5) across agentic and mathematical reasoning benchmarks. The architecture prioritizes intelligence density over parameter count through selective MoE activation and optimized attention mechanisms.

## Key Points

- **Sparse MoE Architecture**: 196B total parameters with 11B active per token decouples model capacity from inference cost, enabling 6-19x decoding cost reduction vs. larger competitors
- **Hybrid Attention**: 3:1 sliding-window-to-full-attention ratio supports 256K context windows efficiently while maintaining reasoning quality
- **Multi-Token Prediction**: 3-way MTP heads enable parallel token verification, achieving 350 tok/s peak throughput on NVIDIA Hopper
- **Agentic Excellence**: 74.4% SWE-bench Verified, 88.2 τ²-Bench, orchestrates 80+ MCP tools with flawless intent-alignment
- **Mathematical Reasoning**: 97.3% AIME 2025 (99.9% with enhanced reasoning), 98.0% HMMT 2025, competitive despite smallest parameter footprint

## Technical Details

**Throughput & Latency**

| Metric | Performance | Hardware |
|--------|-------------|----------|
| Standard throughput | 100-300 tok/s | NVIDIA Hopper |
| Peak throughput | 350 tok/s | NVIDIA Hopper (coding) |
| Local deployment | 20 tok/s | DGX Spark (INT4) |
| Context window | 256K | INT8 KV-cache quantization |

**Architecture Innovations**

- **Head-wise Gated Attention**: Optimizes information flow with numerical stability across layers
- **Augmented query heads**: Increased 64→96 in SWA layers without expanding KV cache footprint
- **MTP-3 integration**: Concurrent token verification during decoding accelerates generation without quality degradation

**Agentic Capabilities**

The model demonstrates sophisticated multi-agent orchestration across domains:
- Web search integration via ReAct architecture (65.3% ResearchRubrics)
- Autonomous codebase analysis for documentation generation
- End-to-end data workflows (39.6% professional benchmarks)
- Flawless tool orchestration across 80+ MCP (Model Context Protocol) tools with intent-alignment

**Known Trade-offs**

- Longer generation trajectories for equivalent quality vs. some competitors
- Reduced robustness during distribution shifts and specialized domains
- Potential identity consistency issues in long-horizon multi-turn dialogues

## Implications

**For Agentic Systems**: Step-3.5-Flash becomes a pragmatic choice for deployment-constrained environments requiring sophisticated tool-use. The 74.4% SWE-bench score matches Opus-class reasoning while consuming a fraction of compute, making it suitable for autonomous code generation workloads on consumer hardware (Mac Studio M4 Max, DGX Spark).

**For Cost-Performance**: The sparse activation strategy fundamentally shifts the parameter-to-performance equation. Organizations can achieve frontier reasoning at 6-19x lower inference cost, justifying local deployment over cloud APIs for latency-sensitive or compliance-restricted applications.

**Trade-off Reality**: The longer generation trajectories suggest the model reasons through problems verbosely rather than concisely. This is acceptable for agentic tasks (where token budget is less constrained) but less suitable for user-facing applications demanding sub-2-second response times. The mathematical reasoning capabilities (AIME 99.9%) indicate effective chain-of-thought integration, but distribution shifts and specialized domains remain vulnerabilities.

**Comparison Context**: Unlike Opus 4.6 (which optimizes for reasoning breadth) or hypothetical GPT-5 (raw capability), Step-3.5-Flash explicitly optimizes for the agent use case—tool-use reliability, cost-efficiency, and local deployability outrank absolute capability ceilings.

## Sources

- [StepFun Blog - Step 3.5 Flash](https://static.stepfun.com/blog/step-3.5-flash/) - Official technical deep-dive
- [NVIDIA NIM Model Card](https://build.nvidia.com/stepfun-ai/step-3.5-flash/modelcard) - Deployment specifications
- [Hugging Face Model Repository](https://huggingface.co/stepfun-ai/Step-3.5-Flash) - Model weights and documentation
- [GitHub Repository](https://github.com/stepfun-ai/Step-3.5-Flash) - Open-source implementation
- [LLM Stats - Benchmarks and Pricing](https://llm-stats.com/models/step-3.5-flash) - Comparative metrics
