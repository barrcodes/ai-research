# ~60GB Models on Coding: GLM 4.7 Flash vs. GPT OSS 120B vs. Qwen3 Coder 30B

| | |
|---|---|
| **Source** | Reddit r/LocalLLaMA |
| **URL** | [reddit.com/r/LocalLLaMA](https://www.reddit.com/r/LocalLLaMA/) |
| **Researched** | 2026-01-26 |

## Overview

Three major open-source coding models have emerged in the ~60GB quantized range, each with distinct architectural approaches and performance characteristics. GLM-4.7-Flash delivers efficient inference with MoE optimization, GPT-OSS-120B provides broad ecosystem support and strong SWE-bench performance, while Qwen3-Coder emphasizes extended context windows and reasoning capabilities.

## Key Points

- **GLM-4.7-Flash (30B active from ~60GB)**: Achieves 59.2% on SWE-bench with 3B active parameters per token, enabling 80 tokens/second inference at 4-bit quantization; optimized for coding-specific tasks
- **GPT-OSS-120B (116.8B total)**: Scores 62.7% on SWE-bench with 5.1B active parameters, fully open-weights release from OpenAI with extensive ecosystem support (Ollama, vLLM, llama.cpp, LM Studio, Apple Metal)
- **Qwen3-Coder (480B MoE / 30B active)**: Ships in multiple configurations (480B activating ~35B, 30B activating ~3B) with 256K context window; stronger on pure reasoning tasks with "Thinking" modes
- **SWE-bench coding performance**: GPT-OSS-120B leads at 62.7%, GLM-4.7-Flash at 59.2%, Qwen3-Coder-480B at 55.4%
- **Architecture efficiency**: All three use Mixture-of-Experts designs to reduce active parameter requirements; GLM-4.7 and Qwen3 most efficient for local deployment

## Technical Details

### Architecture & Performance Trade-offs

**GLM-4.7-Flash** represents the efficiency frontier with 30B total parameters and only 3B active. This MoE architecture enables fast local inference (~80 tokens/second at 4-bit) while maintaining strong coding capability. The model is particularly effective for code completion and tool use scenarios.

**GPT-OSS-120B** trades raw efficiency for absolute performance, requiring 48GB+ VRAM but delivering superior benchmark scores. Its 5.1B active parameters per token via MoE still represent reasonable efficiency gains over dense models. The open-weights nature provides maximum flexibility for fine-tuning and custom deployments.

**Qwen3-Coder** differentiates through extended context (256K tokens) and multi-size scaling. The 480B configuration provides extreme capability for complex code analysis, while the 30B variant offers a middle ground. The "Thinking" mode adds a reasoning dimension absent from direct comparison competitors.

### Benchmark Methodology

All three models assessed via SWE-bench (software engineering benchmark):
- Evaluates code generation and modification across diverse programming tasks
- Considers realistic tool use patterns (file reading, editing, terminal interaction)
- SWE-bench Verified provides stricter evaluation than base SWE-bench

### Inference Characteristics

- **GLM-4.7-Flash**: 4-bit quantized ~59GB, 80 tokens/sec on high-end consumer GPUs
- **GPT-OSS-120B**: Requires 48GB+ VRAM, runs on single 80GB H100/A100; broad hardware support including Apple Metal
- **Qwen3-Coder**: 30B variant quantizes to ~17-20GB, 480B MoE requires similar space as GPT-OSS-120B

## Implications

### For Local Deployment

1. **Resource-constrained environments (24-48GB VRAM)**: GLM-4.7-Flash provides the best balance of capability and speed, sacrificing only 3.5% SWE-bench performance versus the larger models while consuming 20-25% less VRAM

2. **Reasoning-intensive tasks**: Qwen3-Coder's "Thinking" mode addresses complex architectural decisions and multi-step problem solving that standard code generation misses; accept 2-3% SWE-bench penalty for this capability

3. **Production ecosystem requirements**: GPT-OSS-120B's full open-weights and broad tool support (vLLM, llama.cpp, LM Studio) make it optimal for enterprises with existing infrastructure investments; the 3.5% SWE-bench advantage over GLM-4.7 justifies 48GB VRAM requirement for production scenarios

4. **Cost-performance optimization**: For teams running inference clusters, GLM-4.7-Flash's efficiency enables 4-6x throughput improvement (tokens/sec/GB) versus GPT-OSS-120B on identical hardware

### Architecture Decision Points

- **Choose GLM-4.7-Flash if**: Operating in resource-constrained environments, prioritizing inference speed, or running high-volume batch inference
- **Choose GPT-OSS-120B if**: Ecosystem integration is critical, benchmark performance is paramount, or deployment on high-end infrastructure is feasible
- **Choose Qwen3-Coder if**: Extended context windows (256K) unlock novel use cases, reasoning-grade code understanding is required, or multi-size family deployment offers scaling flexibility

## Related

- [GLM-4.7-Flash: The Ultimate 2026 Guide to Local AI Coding Assistant](https://medium.com/@zh.milo/glm-4-7-flash-the-ultimate-2026-guide-to-local-ai-coding-assistant-93a43c3f8db3) - Detailed walkthrough of GLM-4.7-Flash local deployment
- [OpenAI GPT-OSS Benchmarks: How It Compares to GLM-4.5, Qwen3, DeepSeek, and Kimi K2](https://www.clarifai.com/blog/openai-gpt-oss-benchmarks-how-it-compares-to-glm-4.5-qwen3-deepseek-and-kimi-k2) - Comprehensive benchmark analysis
- [GLM-4.7-Flash vs Qwen3-Coder](https://llm-stats.com/models/compare/glm-4-7-flash-vs-qwen3-coder) - Direct model comparison tool
- [GLM-4.7 vs GPT OSS 120B](https://llm-stats.com/models/compare/glm-4-7-vs-gpt-oss-120b) - Efficiency vs performance trade-off analysis
- [gpt-oss-120b vs Qwen3 Coder 30B](https://blog.galaxy.ai/compare/gpt-oss-120b-vs-qwen3-coder-30b-a3b-instruct) - Head-to-head comparison with inference metrics
- [zai-org/GLM-4.7-Flash Hugging Face](https://huggingface.co/zai-org/GLM-4.7-Flash) - Model repository and weights
