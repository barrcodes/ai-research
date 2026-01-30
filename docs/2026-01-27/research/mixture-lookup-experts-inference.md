# Mixture of Lookup Experts: RAM+Disk Hybrid Inference Architecture

| | |
|---|---|
| **Source** | arXiv |
| **URL** | [arxiv.org/abs/2503.15798](https://arxiv.org/abs/2503.15798) |
| **Researched** | 2026-01-27 |

## Overview

Mixture of Lookup Experts (MoLE) eliminates the inference bottleneck in Mixture-of-Experts models by converting expert FFNs into lookup tables indexed by input token IDs. This transforms inference from compute-intensive expert execution into retrieval operations, enabling efficient deployment on resource-constrained hardware with negligible latency overhead compared to dense models—while maintaining MoE-level accuracy.

## Key Points

- **Architecture shift**: Experts accept embedding layer outputs (not intermediate features), enabling FFN→lookup table conversion before inference
- **Communication reduction**: Per-token parameter loading drops from ~57-201M (MoE) to 0.15-0.39M (MoLE)—1/1500 to 1/2000× improvement
- **Latency profile**: Decoding latency remains comparable to dense models across batch sizes; MoE shows substantial latency growth at batch 32+
- **Accuracy parity**: Achieves 41.9% (160M activated) vs MoE-34E 41.8%; 45.7% (410M) vs MoE-34E 46.6% across 8 benchmarks
- **Storage flexibility**: Lookup tables stored in CPU RAM or SSD with "negligible overhead," unlocking edge deployment scenarios

## Technical Details

**Inference Mechanism**:
Router routes tokens to lookup experts. Instead of loading and computing expert FFN weights, MoLE directly retrieves pre-computed results indexed by input ID and transfers them to VRAM. This decouples memory bottleneck from compute.

**Model Configurations Tested**:
| Config | Activated Params | Offloaded Params | Per-Token Load |
|--------|------------------|------------------|----------------|
| MoLE-4E (160M) | 160M | 1.8B | 0.15M |
| MoLE-16E (160M) | 160M | 7.4B | 0.15M |
| MoE-10E (160M) | 160M | 0.3B | 57M |
| MoLE-16E (410M) | 410M | N/A | 0.39M |

**Implementation**:
- Training: `modeling_mole.py` (standard FFN experts)
- Inference: `modeling_mole_rep.py` (reparameterized lookup tables)
- Conversion: `reparameterize.py` transforms checkpoints post-training
- Requires: PyTorch 2.0.1+, Transformers 4.38.2

## Implications

**When to use MoLE**: For practitioners deploying LLMs with memory constraints (edge devices, inference clusters with pinned CPU RAM/SSD storage). Solves the "MoE parameter wall"—experts stay massive, but loading becomes free.

**Trade-off**: Lookup table generation happens offline; incompatible with dynamic expert routing or adaptive computation at inference time. Works best with fixed routing policies trained at scale.

**Competitive advantage**: Offers >50× communication reduction vs MoE with comparable latency to dense models. At 160M activation, enables 7.4B parameter access with only 0.15M per-token transfers—architectural elegance for inference-bound systems.

**Deployment path**: Offload tables to VRAM-constrained machines via disk/CPU storage; batch inference sees minimal degradation. Scales naturally to large expert counts without proportional latency growth (MoE pathology eliminated).

## Related

- [GitHub: JieShibo/MoLE](https://github.com/JieShibo/MoLE) - Official implementation with model checkpoints and training/inference code
- [ICML 2025 Oral Presentation](https://icml.cc/virtual/2025/poster/43620) - Paper accepted as oral presentation
- [Literature Review on arXiv](https://www.themoonlight.io/en/review/mixture-of-lookup-experts) - Independent technical breakdown
- [Mixture of Lookup Key-Value Experts (2512.09723)](https://arxiv.org/abs/2512.09723) - Related variant extending to key-value caching
