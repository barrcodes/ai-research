# Qwen3.5 Model Family: Benchmarks and Agentic Coding Performance

| | |
|---|---|
| **Source** | Alibaba Qwen Team (Official Release) |
| **URL** | [qwen.ai/blog](https://qwen.ai/blog?id=qwen3.5) |
| **Researched** | 2026-02-25 |

## Overview

Alibaba has released the Qwen3.5 family, an open-weight multimodal model series featuring 27B, 35B-A3B, 122B-A10B, 397B-A17B variants with hybrid Gated DeltaNet + sparse Mixture-of-Experts architecture. The 35B-A3B and 122B-A10B variants achieve competitive performance against GPT-5 variants on agentic coding and reasoning benchmarks while maintaining 7x-8x better inference throughput than larger predecessors.

## Key Points

### Architecture & Efficiency

- **Hybrid Architecture**: Gated DeltaNet (linear attention) combined with sparse Mixture-of-Experts (256 total experts, 8-9 activated per forward pass) instead of dense attention
- **Activation Sparsity**: Only 3B/10B/17B parameters activated per forward pass despite 35B/122B/397B total parameters respectively
- **Throughput Advantage**: Qwen3.5-397B-A17B achieves 8.6x throughput vs Qwen3-Max at 32k context, 19.0x at 256k context
- **Context Extension**: Native 262,144 token support, extensible to 1,010,000 tokens without retraining
- **Thinking Mode**: Native chain-of-thought reasoning baked into model weights (no parameter overhead for reasoning)

### Benchmark Performance vs GPT-5 Variants

**Qwen3.5-35B-A3B vs GPT-5-mini (2025-08-07)**:
- **Agentic Capabilities** (where it shines):
  - TAU2-Bench: 81.2 vs 69.8 (agent orchestration tasks)
  - BFCL-V4: 67.3 vs 55.5 (function calling)
  - Browsecomp-zh: 69.5 vs 49.5 (multilingual search agents)
- **Coding**:
  - SWE-bench Verified: 69.2 vs 72.0 (minor gap on full code tasks)
  - Terminal Bench 2: 40.5 vs 31.9 (terminal-based reasoning)
  - LiveCodeBench v6: 74.6 vs 80.5 (slightly behind)
- **Knowledge**:
  - MMLU-Pro: 85.3 vs 83.7 (slightly stronger)
  - C-Eval: 90.2 vs 82.2 (stronger on Chinese tasks)
  - SuperGPQA: 63.4 vs 58.6 (consistent advantage)

**Qwen3.5-122B-A10B vs GPT-5-mini**:
- **Agentic Capabilities**:
  - TAU2-Bench: 79.5 vs 69.8 (substantial improvement)
  - BFCL-V4: 72.2 vs 55.5 (strong function calling)
  - Terminal Bench 2: 49.4 vs 31.9 (48% higher)
- **Coding**:
  - SWE-bench Verified: 72.0 vs 72.0 (parity with GPT-5-mini)
  - LiveCodeBench v6: 78.9 vs 80.5
  - Terminal Bench 2: 49.4 vs 31.9 (excels at tool use)

### Multimodal & Agentic Strengths

- **Visual Agent Performance**: Qwen3.5-35B-A3B exceeds GPT-5-mini on visual reasoning:
  - VlmsAreBlind: 97.0 vs 75.8 (exceptional spatial understanding)
  - ScreenSpot Pro: 68.6 vs unavailable
  - AndroidWorld: 71.1 vs unavailable (mobile agent automation)
- **Tool Calling/Function Execution**: V* benchmark (tool integration + execution):
  - Qwen3.5-122B: 93.2/90.1 (with/without code interpreter) vs GPT-5-mini 71.7
  - Qwen3.5-35B: 92.7/89.5 vs GPT-5-mini 71.7
- **Search Agent Capabilities**:
  - Qwen3.5-122B Browsecomp: 63.8 (simple) / 69.9 (Chinese)
  - Qwen3.5-35B TAU2-Bench: 81.2 vs GPT-5-mini 69.8

### Multilingual Coverage

- **Expansion**: From 119 to 201 languages/dialects
- **Vocabulary**: Increased from 150k to 250k tokens (10-60% encoding/decoding efficiency gains across most languages)
- **Chinese Performance**: Consistently outperforms on C-Eval, WMT24++ translation tasks

### Training Infrastructure Innovations

- **Heterogeneous Multimodal Training**: Decoupled parallelism strategies for vision vs language to avoid bottlenecks
- **Near 100% Training Efficiency**: Mixed text-image-video training achieves same throughput as text-only training
- **Asynchronous RL Framework**:
  - Supports million-scale agent scaffolds and environments
  - 3-5x end-to-end speedup through FP8 training, speculative decoding, rollout router replay
  - Disaggregated train-inference architecture with dynamic load balancing
  - Fine-grained fault recovery for stable scaling
- **FP8 Pipeline**: ~50% activation memory reduction, >10% speedup while maintaining stability

## Technical Details

### Model Variants Comparison

| Aspect | 27B | 35B-A3B | 122B-A10B |
|--------|-----|---------|-----------|
| Total Parameters | - | 35B | 122B |
| Activated | - | 3B | 10B |
| Hidden Dimension | - | 2048 | 3072 |
| Layers | - | 40 | 48 |
| Context (Native) | - | 262k | 262k |
| SWE-bench Verified | 72.4 | 69.2 | 72.0 |
| TAU2-Bench | 79.0 | 81.2 | 79.5 |
| Tool Calling (V*) | 93.7/89.0 | 92.7/89.5 | 93.2/90.1 |

### RL Scaling for Agentic Capabilities

Qwen3.5 distinguishes itself through extensive RL across:
- **General Agent Benchmarks**: BFCL-V4, VITA-Bench, DeepPlanning, Tool-Decathlon, MCP-Mark
- **Search Agent Tasks**: Web search, information retrieval with context management
- **Visual Agents**: GUI automation (ScreenSpot Pro), mobile agents (AndroidWorld), desktop workflows
- **Coding Agents**: Terminal-based reasoning, tool use integration, multi-turn code generation
- **Emphasis on Generalization**: Difficulty scaling and diverse task distributions rather than narrow metric optimization

### Inference Optimization

- **Multi-Token Prediction (MTP)**: Speculative decoding with 3-4 draft tokens for 30-40% throughput improvement
- **Dynamic Context Folding**: Search agents implement context-folding strategy at 256k tokens to manage long interactions
- **Sparse Expert Routing**: Only 8-9 of 256 experts active per token reduces compute and memory

## Implications

### For Practitioners

**When to Use Qwen3.5-35B-A3B (3B activated)**:
- Multi-turn agentic workflows (chat + reasoning + tools)
- Multilingual applications, especially with non-Latin scripts
- Edge/on-device deployment where throughput matters (7x better than 122B-A10B)
- Cost-constrained agentic systems (smallest usable model in family)
- Visual understanding tasks requiring spatial reasoning

**When to Use Qwen3.5-122B-A10B (10B activated)**:
- SWE-bench/code-generation frontiers (parity with GPT-5-mini on full tasks)
- Terminal-based tool automation (49.4 on Terminal Bench 2 vs 31.9 for GPT-5-mini)
- Production tool-calling workloads (93.2+ on V* benchmark)
- Reasoning-intensive tasks (HMMT: 91.4 vs 89.2 for GPT-5-mini)
- When throughput allows (8.6x better than previous generation at same quality)

**Critical Trade-offs**:
- **Qwen3.5-35B trades some code quality for agentic speed**: SWE-bench -3 points vs 122B variant, but TAU2-Bench +1.7 points (agent orchestration)
- **Thinking mode enabled by default**: Increased latency but essential for complex reasoning; can disable for lower-latency inference
- **Context Management Required**: Search agents need context-folding strategies—naive long context accumulation degrades agent performance
- **No comparison to GPT-5 full/Claude-4.5-Opus**: Qwen3.5-397B-A17B needed for frontier performance; publicly released variants target 35B-122B space

### Architecture Decision Points

1. **Sparse Activation Hierarchy**: The 8-9 router experts + 1 shared expert design differs from competitors. Routing stability with only 9 experts per token is tighter than DeepSeek's 8-8 or other designs—watch for pathological routing cases under long-context stress.

2. **Early Multimodal Fusion**: Unlike models that bolt vision on top, Qwen3.5 fuses text-vision tokens during pretraining. This trades flexibility (harder to add modalities post-hoc) for native spatial-linguistic understanding. Evidence: VlmsAreBlind 97.0 vs GPT-5-mini 75.8.

3. **FP8 Throughout Training**: Rare adoption of FP8 for activations + routing + GEMM (not just weights). Enables 50% memory reduction on activations but requires careful layer monitoring. Relevant for companies building custom RL loops.

4. **Asynchronous RL at Million-Scale**: Fully disaggregated train-inference means no synchronization barriers. Enables expensive RL environments (e.g., code execution sandbox) without blocking model updates. Critical for production agentic systems but requires infrastructure investment.

### Organizational Readiness

- **Open Weight**: Full weights on Hugging Face (Apache 2.0 license)—legally safe for commercial deployment unlike some alternatives
- **Inference Framework Support**: SGLang, vLLM, KTransformers ready with native optimizations
- **Thinking Mode**: Integrated via API or built-in—no external wrapper needed for reasoning workflows
- **Production APIs**: Alibaba Cloud ModelStudio provides Qwen3.5-Plus with 1M context, built-in tools, adaptive tool use—lower deployment friction than self-hosted

## Sources

- [Qwen3.5 Blog Post](https://qwen.ai/blog?id=qwen3.5) - Official release announcement with comprehensive benchmarks
- [Qwen/Qwen3.5-35B-A3B on Hugging Face](https://huggingface.co/Qwen/Qwen3.5-35B-A3B) - Model card and benchmark tables
- [Qwen/Qwen3.5-122B-A10B on Hugging Face](https://huggingface.co/Qwen/Qwen3.5-122B-A10B) - Model card with inference guides
- [r/LocalLLaMA Top Posts (2026-02-25)](https://old.reddit.com/r/LocalLLaMA/top/?t=day) - Community discussion of benchmarks and agentic performance
