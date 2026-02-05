# vLLM-Omni: Fully Disaggregated Serving for Any-to-Any Multimodal Models

| | |
|---|---|
| **Source** | arXiv / vLLM Project |
| **URL** | [arxiv.org/abs/2602.02204](https://arxiv.org/abs/2602.02204) |
| **Researched** | 2026-02-05 |

## Overview

vLLM-Omni is a fully disaggregated execution framework that decomposes complex multimodal models (text, image, video, audio) into independent pipeline stages. Rather than forcing everything through a single serving engine, it treats each stage as a separate component with its own scheduler and resource allocation. This architectural shift achieves up to 91.4% JCT reduction for models like Qwen3-Omni and up to 12.97× throughput improvements on specific components.

## Key Points

- **Stage Abstraction Graph**: Decompose any-to-any architectures into interconnected stages where nodes are model components (encoders, LLM, diffusion generators) and edges define data routing transformations between them.

- **Disaggregated Execution**: Each stage runs independently with dedicated LLM or diffusion engines, its own KV cache manager, and per-stage request batching—eliminating global synchronization bottlenecks that plague monolithic designs.

- **Flexible Resource Allocation**: GPU allocation scales per-stage rather than per-model, enabling fine-grained resource distribution (e.g., allocate more GPUs to the bottleneck diffusion stage, fewer to lightweight encoders).

- **Unified Inter-Stage Connectors**: Single abstraction for passing embeddings, hidden states, and modality-specific tensors between heterogeneous stages without manual serialization or coupling.

## Technical Details

**Architecture Layers**:
- **Modality Encoders** (ViT, Whisper, etc.): Encode multimodal inputs in parallel
- **LLM Core**: vLLM-based autoregressive text and hidden state generation
- **Modality Generators**: High-performance DiT and specialized decoding heads for output generation

**Scheduling & Batching**: Per-stage request batching maximizes utilization at each layer without coordinating across stages. An orchestrator manages execution flow and inter-stage routing. No explicit synchronization or global batching window—stages progress independently.

**Execution Graph Compilation**: Optional compilation step that optimizes tensor routing and batching patterns across stages before runtime.

**Performance Benchmarks**:
- Qwen3-Omni: 91.4% JCT reduction, 90.7% RTF reduction
- Qwen2.5-Omni: 61.4-61.6% improvements
- BAGEL text-to-image: 2.40× speedup; image-to-image: 3.72×
- MiMo-Audio: 11.58× with graph compilation
- Diffusion models: 1.26× vs. Diffusers baseline

## Implications

**Architectural Shift**: This disaggregated model challenges the "single engine" paradigm (vLLM for text, Diffusers for images). For teams building multimodal systems, this suggests decomposing inference pipelines into stages is architecturally superior to trying to force heterogeneous models into one serving framework.

**Resource Optimization Trade-off**: Disaggregation requires managing multiple schedulers and inter-stage buffers, increasing operational complexity. The payoff: much better utilization when stages have unbalanced computational demands (common with vision encoders + LLM + diffusion).

**When to Use**: Critical for production multimodal serving where:
- Models combine fundamentally different compute patterns (e.g., text autoregression + image diffusion)
- Input/output modalities vary (some requests text-only, some multi-modal)
- You need to scale stages independently based on demand

**Alternatives**: Monolithic frameworks (unified API, simpler ops) work if all requests follow the same pipeline and stages have balanced compute. Manual orchestration (multiple services) avoids framework lock-in but loses automatic batching and resource optimization.

**Implementation Path**: vLLM-Omni is open-source; early adopters can experiment with decomposing existing pipelines into stage graphs and measuring JCT improvements on representative workloads.

## Sources

- [arXiv: vLLM-Omni paper (2602.02204)](https://arxiv.org/abs/2602.02204)
- [vLLM-Omni GitHub repository](https://github.com/vllm-project/vllm-omni)
- [vLLM-Omni official documentation](https://docs.vllm.ai/projects/vllm-omni/en/latest/)
- [vLLM Blog announcement](https://blog.vllm.ai/2025/11/30/vllm-omni.html)
