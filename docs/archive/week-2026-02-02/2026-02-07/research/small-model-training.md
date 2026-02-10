# Training 1.8M Parameter Models from Scratch: Architectural Innovations

| | |
|---|---|
| **Source** | Reddit - r/LocalLLaMA |
| **URL** | [old.reddit.com/r/LocalLLaMA/comments/1qym566/](https://old.reddit.com/r/LocalLLaMA/comments/1qym566/i_trained_a_18m_params_model_from_scratch_on_a/) |
| **Researched** | 2026-02-07 |

## Overview

A practitioner trained a 1.8M parameter model from scratch on ~40M tokens using a custom architecture called Strawberry, achieving final train loss of 3.5 and validation loss of 3.8. The key innovation is a "Retention" mechanism that dynamically generates attention weights at inference time rather than learning them as parameters, enabling deeper architectures without parameter overhead. The work validates parameter-efficient design patterns and introduces hybrid attention mechanisms combining linear attention (for global context) with standard multi-head attention (for local context).

## Key Points

- **Retention Mechanism**: Weights for Query-Key-Value projections are dynamically generated based on input rather than learned, eliminating the parameter cost of attention depth scaling while maintaining expressiveness through learned mini-FFN layers between attention heads.

- **Efficient Depth**: The architecture stacks multiple attention layers without parameter growth by computing weights in real-time. This addresses the fundamental trade-off between model depth (which improves expressiveness) and parameter count (which increases training/inference cost).

- **Hybrid Attention Strategy**: Combines Apple's AFT linear attention for global context with standard multi-head attention for local context—a practical pattern for efficiency-constrained scenarios where full quadratic attention becomes prohibitive.

- **Data-Efficient Training**: Trained on only 40M tokens (roughly 30M characters) using a manually curated, multi-domain dataset (Wikipedia, game lore, code, research papers, transcripts). Final loss of 3.8 on validation data is reasonable for this parameter/data regime.

- **Non-Linearity Requirement**: Critical architectural finding: stacking attention layers without intervening non-linear projections (FFN) causes performance degradation. This validates the necessity of feed-forward layers beyond their raw capacity—they provide essential gradient flow and feature transformation.

## Technical Details

**Model Architecture**:
- Vocabulary size: 8,192 tokens
- Context window: 256 tokens
- Batch size: 16 (4,096 tokens per step)
- Training steps: 10,000 (40M total tokens)
- Layers: 2 Retention + 3 supporting layers
- Attention heads: 6
- Embedding dimension: 96
- QKV/FFN dimension: 384

**Retention Mechanism Specifics**:
- Generates attention weights dynamically based on input features
- Bypasses learned QKV parameter matrices typical in standard attention
- Mini-FFN layers (learned parameters) inserted after each attention head for non-linearity
- Formulation similar to linear attention but with input-dependent generation

**Optimizer Configuration**:
- Adam variant with beta1=0.9, beta2=0.99
- Learning rate: 0.002 with cosine decay
- Warmup: 500 steps (5% of total)
- Weight decay: 0.001
- No Muon optimizer (uses standard Adam)

**Dataset Composition**:
- Wikipedia texts (topics, personalities, companies)
- Game narratives (GTA, RDR, Last of Us, Mafia, Cyberpunk)
- Game scripts and dialogue
- YouTube video transcripts
- Personal codebases (Python, C#, C++, JavaScript focused)
- Research papers and AI-focused academic content
- Tokenized with 8K vocabulary derived from Andrej Karpathy's tokenizer

## Implications

**For Model Architecture Design**:
1. Parameter-efficient attention mechanisms are viable at micro-scale, suggesting viability for edge deployment and resource-constrained inference.
2. The retention mechanism challenges the assumption that all attention projections must be learned parameters—input-dependent generation is architecturally sound.
3. The FFN-after-attention pattern is not optional for depth scaling; intervening non-linearity is architecturally necessary when stacking attention layers.

**For Training Small Models**:
1. Manual, high-quality dataset curation is achievable at this scale and may outperform naive large-scale scraping for foundational research.
2. Loss trajectories at 1.8M params with 40M tokens provide a usable baseline for evaluating architectural changes without massive compute overhead.
3. Batch size of 16 with 256 context length (4,096 tokens/step) is practical for consumer hardware and still provides sufficient training signal.

**For Production Considerations**:
1. Hybrid attention strategies (linear for global, local for windowed) reduce inference compute while maintaining expressiveness—applicable to on-device and edge scenarios.
2. The approach demonstrates that meaningful model capability can emerge from careful architectural choices rather than scale alone, relevant for latency-constrained systems.
3. Real-time weight generation in attention could enable dynamic context-aware behavior and reduce static parameter bloat.

**For Further Research**:
1. Mixture of Attention Experts (planned implementation) extends the local/global hybrid strategy to enable expert specialization per attention head.
2. Scaling the retention mechanism to larger parameter counts (10M-100M range) would validate whether the efficiency gains persist across orders of magnitude.
3. Comparison with standard transformer variants at identical parameter count would quantify the accuracy trade-offs of retention-based attention.

## Sources
- [r/LocalLLaMA discussion: "I trained a 1.8M params model from scratch on a total of ~40M tokens"](https://old.reddit.com/r/LocalLLaMA/comments/1qym566/i_trained_a_18m_params_model_from_scratch_on_a/)
- [Strawberry GitHub repository](https://github.com/SrijanSriv211/Strawberry)
- Author's tokenizer implementation from Andrej Karpathy's tokenizer methodology
