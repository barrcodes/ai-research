# GLM-5 Technical Report

| | |
|---|---|
| **Source** | Zhipu AI / Z.AI |
| **URL** | [github.com/zai-org/GLM-5](https://github.com/zai-org/GLM-5) |
| **Researched** | 2026-02-18 |

## Overview

GLM-5 is a 744B-parameter Mixture of Experts (MoE) model from Zhipu AI (Z.AI) released February 11, 2026, representing a 2.1x scale-up from GLM-4.5 with 40B active parameters per token. Trained entirely on Huawei Ascend hardware using MindSpore framework, GLM-5 achieves frontier-level performance on reasoning, coding, and agentic tasks while demonstrating complete independence from US semiconductor hardware—a strategically significant milestone for non-US AI infrastructure viability.

## Key Points

- **Architecture**: 744B total parameters with 256 expert layers, top-8 routing (~5.9% sparsity), activating 40B per token—up from GLM-4.5's 355B/32B
- **Context Window**: 200K input tokens; 131K output tokens (among industry's highest)
- **Attention Mechanism**: DeepSeek Sparse Attention (DSA) for efficient long-context processing without dense attention overhead
- **Training Scale**: 28.5T tokens pre-training (up from 23T), with novel "Slime" asynchronous RL framework for post-training
- **Hardware Independence**: Trained exclusively on Huawei Ascend 910 series with MindSpore—zero dependency on NVIDIA GPUs
- **Benchmark Leadership**: #1 open-source model globally on BrowseComp (75.9), Vending Bench 2 ($4,432 efficiency score), first on multiple agentic benchmarks
- **Coding Performance**: 77.8% on SWE-bench Verified (approaching Claude Opus 4.5's 80.9%), 98% frontend build success on CC-Bench-V2
- **Pricing**: ~$0.11/M input tokens vs GPT-5.2's $1.75 (16x cheaper) and Claude Opus 4.5's $5.00 (45x cheaper)
- **Open Release**: MIT-licensed weights available on Hugging Face; OpenRouter integration; GLM Coding Plan subscription ($10–$80/month for tooling)

## Technical Details

### Model Architecture

GLM-5 employs a Mixture of Experts design with 256 expert layers where each token routes to exactly 8 experts, yielding ~5.9% activation sparsity. This architecture scales from GLM-4.5's 355B parameters (32B active) to 744B (40B active) while maintaining inference efficiency through sparse routing.

The key innovation is **DeepSeek Sparse Attention (DSA)**, which enables the model to process 200K-token contexts without the O(n²) computational complexity of dense attention. This is critical for long-horizon agentic tasks requiring multi-document context fusion.

### Training Infrastructure

GLM-5 was trained entirely on **Huawei Ascend 910 series** chips—a strategic decision with geopolitical ramifications given US export controls on advanced NVIDIA GPUs. The use of **MindSpore** (Huawei's open-source deep learning framework) instead of PyTorch/CUDA demonstrates that frontier-scale AI training is viable on non-US hardware at production scale (744B parameters).

**Slime RL Framework**: Post-training uses an asynchronous reinforcement learning infrastructure called "Slime" that substantially improves training throughput and efficiency. This enables the model to produce 3,000–6,000 messages per RL run (~60–100M output tokens), optimizing for long-horizon planning and tool use—critical for agentic engineering.

Pre-training corpus expanded to **28.5T tokens** from 23T, focusing on diverse, high-quality data for engineering, coding, and reasoning tasks.

### Capability Domains

1. **Agentic Engineering**: Paradigm shift from "vibe coding" to delivery-first task decomposition. Agent Mode automatically orchestrates tools and executes workflows to produce ready-to-use results (full-stack apps, data pipelines, contract extraction).

2. **Software Engineering**: 77.8% on SWE-bench Verified (multi-step code generation + reasoning). CC-Bench-V2 shows 98% frontend build success and 65.6% accuracy on large repository tasks.

3. **Advanced Reasoning**: 50.4 on Humanity's Last Exam (with tools—outscoring Claude Opus 4.5's 43.4), 89.7 on τ²-Bench, 75.9 on BrowseComp (#1 globally).

4. **Long-Context Processing**: 200K context window handles entire codebases, research paper collections, and video transcripts in single sessions. 131K max output supports comprehensive synthesis tasks.

5. **Creative Writing**: Noted improvement in stylistic versatility and nuance over GLM-4.7, supporting narrative, technical documentation, and marketing copy generation.

### Performance Benchmarks (vs Frontier Models)

| Benchmark | GLM-5 | Claude Opus 4.5 | GPT-5.2 | Gemini 3 Pro |
|-----------|-------|-----------------|---------|-------------|
| Humanity's Last Exam (w/ Tools) | **50.4** | 43.4 | 45.8 | 45.5 |
| SWE-bench Verified | 77.8% | **80.9%** | 76.2% | 80.0% |
| BrowseComp | **75.9** | 67.8 | 59.2 | 65.8 |
| Vending Bench 2 (efficiency) | **$4,432** | $4,967 | $5,478 | $3,591 |
| Terminal-Bench 2.0 | 56.2% | 59.3% | 54.2% | 54.0% |

GLM-5 achieves best-in-class on agentic/reasoning tasks and efficiency, while remaining competitive on code generation despite slightly trailing on SWE-bench Verified.

### Deployment Characteristics

- **Throughput**: ~76 tokens/sec output; ~55 tokens/sec on constrained inference
- **Time-to-first-token (TTFT)**: ~1.54s (suitable for batch/agentic workflows; minor overhead on short chat)
- **Text-only**: No vision/audio support (multimodal variants GLM-4.6V/4.5V available separately)
- **Inference Cost**: Dramatically lower than dense models due to 40B active parameters vs 744B total

## Implications

### For Practitioners

1. **Cost-Performance Trade-off**: GLM-5 enables teams to replace Claude Opus 4.5 ($5/M input) with 45x cheaper alternative while maintaining competitive coding/reasoning performance. Critical for cost-sensitive agentic systems at scale.

2. **Agentic Paradigm**: The shift from conversational models to delivery-first Agent Mode reframes LLM utility—models become autonomous task executors rather than consultants. This requires architectural rethinking of task decomposition and tool orchestration.

3. **Hardware Independence**: For enterprises with supply-chain constraints or geopolitical risk concerns, GLM-5 validates that competitive inference can run on Ascend/non-NVIDIA infrastructure. Implications for deployment flexibility and regulatory compliance.

4. **Long-Context Advantage**: 200K context + 131K output creates new possibilities for document-in-document-out workflows (e.g., code refactoring on entire repositories, multi-document synthesis) that are cost-prohibitive with GPT-5.2 (400K context but 15x+ higher pricing).

5. **Sparse MoE Architecture**: As MoE becomes the frontier standard (GLM-5, Qwen 3.5, Kimi K2.5), practitioners should expect:
   - Reduced per-token inference cost vs dense transformers
   - Expert routing overhead (~1.5s TTFT) is acceptable for batch/agentic tasks but problematic for real-time chat
   - Router collapse risk and load balancing become critical engineering concerns

6. **Open Weights Value**: MIT license + public weights on Hugging Face enable:
   - Fine-tuning for domain-specific tasks (financial document parsing, code refactoring)
   - On-premise deployment (avoiding API rate limits and data residency concerns)
   - Competitive moat erosion for API-only providers

### For Architecture Decisions

- **When to Use GLM-5**: Agentic workflows, code generation, long-context synthesis, cost-sensitive batch processing, organizations requiring open weights or non-US hardware
- **When to Avoid**: Sub-second latency requirements (chat bots, real-time assistants), multimodal reasoning, tasks requiring largest reasoning capacity (Humanity's Last Exam suggests Claude Opus 4.5 retains slight edge)

### Geopolitical Significance

GLM-5's training on Huawei Ascend hardware demonstrates that US semiconductor export controls have not prevented China from reaching frontier AI capability. This reshapes:
- Global AI power balance (no longer US-monopoly)
- Semiconductor competition (validates non-NVIDIA pathways)
- Export control policy effectiveness (likely driving acceleration of domestic chip development)

## Sources

- [Sai Dheeraj Gummadi - GLM-5 Deep Dive: 745B MoE Beast Crushing SWE-Bench](https://medium.com/@gsaidheeraj/glm-5-deep-dive-745b-moe-beast-crushing-swe-bench-code-benchmarks-5757a3022251) - Comprehensive technical breakdown with code examples and benchmark analysis
- [Digital Applied - GLM-5 Released: 744B MoE Model vs GPT-5.2 & Claude Opus 4.5](https://www.digitalapplied.com/blog/zhipu-ai-glm-5-release-744b-moe-model-analysis) - Comparative analysis, hardware independence details, pricing breakdown
- [Z.AI GitHub Repository](https://github.com/zai-org/GLM-5) - Official open-source release
- [Z.AI Developer Documentation](https://docs.z.ai/guides/llm/glm-5) - API integration and deployment guides
- [Hugging Face - zai-org/GLM-5](https://huggingface.co/zai-org/GLM-5) - Model weights download (MIT license)
