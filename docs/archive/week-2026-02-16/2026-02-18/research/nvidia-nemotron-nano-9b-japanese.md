# NVIDIA Nemotron 2 Nano 9B Japanese

| | |
|---|---|
| **Source** | NVIDIA / Hugging Face Blog |
| **URL** | [huggingface.co/blog/nvidia/nemotron-nano-9b-v2-japanese-ja](https://huggingface.co/blog/nvidia/nemotron-nano-9b-v2-japanese-ja) |
| **Researched** | 2026-02-18 |

## Overview

NVIDIA released Nemotron-Nano-9B-v2-Japanese, achieving first-place performance in the ≤10B parameter category on Japan's Nejumi Leaderboard 4. The model combines a Transformer-Mamba hybrid architecture with 9B parameters, designed specifically for on-premises deployment with robust agentic capabilities—tool calling, code generation, math reasoning—coupled with native Japanese proficiency.

## Key Points

- **Performance**: 6x throughput improvement over comparable open-source Japanese models; SOTA ranking across 40+ benchmarks measuring language ability, agent capabilities, and alignment
- **Architecture**: Transformer-Mamba hybrid (proven from base Nemotron-Nano-9B-v2) optimized for multi-turn conversations and structured function generation
- **Synthetic Data Innovation**: Nemotron-Personas-Japan dataset (600M personas) uses culturally-grounded demographic distribution for high-quality SDG without redundancy
- **Deployment Profile**: 9B parameters enable full fine-tuning on modest infrastructure; edge GPU compatible; viable for enterprise on-premise and edge scenarios
- **Agentic Capabilities**: Robust tool calling, structured output generation, and function invocation—differentiator versus other compact Japanese models

## Technical Details

**Training Pipeline**:
- Continued pre-training on Japanese OSS Corpus (Wikipedia, fineweb-2, aozorabunko, web corpus) plus Nemotron-CC-v2.1
- Supervised fine-tuning with Nemotron-Post-Training-v3 dataset emphasizing tool-calling scenarios
- Tools: Megatron-LM for pre-training/SFT; NeMo Curator for data preprocessing

**Key Benchmarks** (Nejumi Leaderboard 4):
- Outperforms Qwen3-8B across fundamental comprehension, code generation, math reasoning
- Measures: instruction following, bias/toxicity detection, truthfulness, robustness
- Evaluation depth: ~40 distinct benchmarks across linguistic and agentic dimensions

**Personas Dataset** (Reproducible Advantage):
- 600M synthetic personas grounded in Japanese demographic distributions
- Preserves geographic diversity and personality variation reflecting population coherence
- Scales SDG efficiently without quality degradation

## Implications

**For Production Deployment**: This model bridges the gap between efficiency and capability—9B parameters fits enterprise on-prem constraints while tool-calling reliability enables autonomous agent patterns. Suitable for customer service automation, internal tooling, and multi-agent workflows without cloud dependency.

**For Custom Solutions**: Strong foundation for domain-specific fine-tuning. The Nemotron-Personas-Japan approach is reproducible—teams can apply similar persona-based SDG strategies for other languages or domains, improving data quality consistency.

**Architectural Tradeoff**: Transformer-Mamba hybrid adds complexity vs. pure Transformer, but the 6x throughput gain justifies this for latency-sensitive agentic applications. Competitive advantage lies in combining speed with structured output reliability—both critical for production agents.

**Availability**: Model, personas dataset (CC BY 4.0), and NeMo Framework fine-tuning tools are open-source. Lowers barrier to Japanese-language agentic systems in enterprises avoiding proprietary APIs.

## Sources
- [NVIDIA Nemotron-Nano-9B-v2-Japanese on Hugging Face](https://huggingface.co/nvidia/NVIDIA-Nemotron-Nano-9B-v2-Japanese) - Model weights and documentation
- [Nemotron-Personas-Japan Dataset](https://huggingface.co/datasets/nvidia/Nemotron-Personas-Japan) - Synthetic personas for SDG (CC BY 4.0)
- [NeMo Framework](https://github.com/NVIDIA/NeMo) - Training and fine-tuning toolkit
