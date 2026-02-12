# Unsloth GLM-5 GGUF Quantization and Deployment

| | |
|---|---|
| **Source** | Unsloth Documentation |
| **URL** | [unsloth.ai/docs/models/glm-5](https://unsloth.ai/docs/models/glm-5) |
| **Researched** | 2026-02-12 |

## Overview

Unsloth's GLM-5 GGUF quantization dramatically reduces memory footprint from 1.65TB (full 744B parameters) to 241GB (2-bit dynamic) or 176GB (1-bit dynamic)—85-89% reduction. This enables practical deployment on consumer hardware while maintaining inference quality through Dynamic 2.0 quantization that intelligently selects per-layer quantization strategies based on model architecture.

## Key Points

- **Model Specifications**: GLM-5 is a 744B parameter (40B active) MoE model with 200K context window, trained on 28.5T tokens
- **Quantization Results**:
  - Full model: 1.65TB baseline
  - UD-IQ2_XXS (2-bit dynamic): 241GB (-85%)
  - UD-TQ1_0 (1-bit dynamic): 176GB (-89%)
- **Memory Requirements**:
  - 2-bit quant: Fits on 256GB unified memory Mac, 1x24GB GPU + 256GB RAM with MoE offloading
  - 1-bit quant: Requires ~180GB RAM
  - 8-bit: Requires 805GB RAM
- **Dynamic 2.0 Quantization**: Per-layer selection of quantization type rather than uniform quantization; combinations differ per-layer and per-model. Intelligently upcasts important layers to 8/16-bit while quantizing non-critical layers to 1-2 bits
- **Inference Frameworks**: Works on llama.cpp (requires PR #19460), Ollama, Open WebUI, and any GGUF-compatible engine

## Technical Details

**Quantization Architecture:**
- Dynamic 2.0 selectively quantizes every possible layer with model-specific schemes
- Employs >1.5M token calibration dataset (high-quality, hand-curated)
- Uses KL Divergence as quantization metric (not perplexity) to minimize "flips" (incorrect→correct answer changes)
- Addressable layer-specific offloading via regex patterns to CPU for fine-grained optimization

**Deployment Configuration:**
- llama.cpp requires custom PR for GLM-5 support; standard builds insufficient
- MoE layer offloading patterns: `-ot ".ffn_.*_exps.=CPU"` offloads all MoE; granular patterns support partial offloading
- `--fit on` flag maximizes GPU/CPU utilization; handles automatic hardware detection
- Context window capped at 202,752 tokens (200K specification)

**Inference Performance Tuning:**
- Default settings (temperature=1.0, top_p=0.95, max_tokens=131,072) for general tasks
- Tool-calling preset (temperature=1.0, top_p=0.95, max_tokens=16,384) for structured outputs
- SWE-Bench verified settings (temperature=0.7, top_p=1.0, max_tokens=16,384)
- Flash attention enabled (`--flash-attn on`) for efficiency

**Production Deployment:**
- Use `llama-server` for OpenAI-compatible API endpoint
- Default port binding (8001) with alias routing
- Integrates with OpenAI Python client: queries against local server appear identical to cloud API
- Example: Snake game generation demonstrates capability on complex code synthesis

## Implications

**For Practitioners:**

1. **Hardware Economics**: 85%+ size reduction makes on-premises deployment economically viable. Commodity hardware (dual 24GB GPUs + 256GB RAM) can serve production GLM-5 workloads without expensive cloud infrastructure.

2. **Layer-Specific Optimization**: Dynamic 2.0's per-layer quantization strategy is architecturally novel—moves beyond uniform quantization dogma. Teams can trade off accuracy at specific layers (e.g., quantize FFN projections aggressively while preserving attention heads at higher precision).

3. **Routing Complexity vs. Accuracy**: MoE architecture with selective CPU offloading introduces operational complexity. The ability to offload only gate/up-projection layers vs. all MoE operations suggests careful profiling required per deployment environment. Cold-start CPU offloading will degrade latency expectations.

4. **Calibration Dataset Matters**: Hand-curated >1.5M token dataset (distinct from standard Wikipedia benchmarks) improves chat performance—indicates that quantization quality depends on domain-specific calibration. Generic imatrix approaches using Wikipedia will underperform for instruction-tuned models.

5. **KL Divergence vs. Accuracy Metrics**: Paper cites "Accuracy is Not All You Need"—KL divergence correlates with answer-flip reduction rather than raw accuracy deltas. This affects how teams should evaluate quantized variants; perplexity is insufficient. Expect some correct answers becoming incorrect and vice versa during quantization, offsetting raw accuracy measurements.

6. **Inference Latency Profile**: SSD/HDD offloading supported but "slower" (undefined in documentation). 1-bit quantization trades maximum compression for potentially degraded real-time performance; 2-bit represents the practical sweet spot for latency-sensitive deployments.

7. **API Compatibility**: OpenAI-compatible endpoint abstraction means quantized models integrate transparently into existing tooling. This dramatically reduces migration friction from proprietary inference services.

## Sources

- [GLM-5: How to Run Locally Guide | Unsloth Documentation](https://unsloth.ai/docs/models/glm-5)
- [Unsloth Dynamic 2.0 GGUFs | Unsloth Documentation](https://docs.unsloth.ai/basics/unsloth-dynamic-2.0-ggufs)
- [GLM-5-GGUF on Hugging Face](https://huggingface.co/unsloth/GLM-5-GGUF)
