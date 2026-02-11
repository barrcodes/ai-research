# Probing LLM Personality: 7 Behavioral Axes in Hidden States Across 6 Open-Weight Models

| | |
|---|---|
| **Source** | Mood Axis Project / Reddit Discussion |
| **URL** | [github.com/yunoshev/mood-axis](https://github.com/yunoshev/mood-axis) |
| **Researched** | 2026-02-11 |

## Overview

A novel interpretability study probed 6 open-weight LLMs (7B-9B parameters) to extract measurable personality dimensions directly from hidden state representations. By applying linear projection vectors trained on contrasting instruction pairs, researchers mapped model behaviors onto 7 interpretable axes: warmth, patience, confidence, proactivity, empathy, formality, and verbosity. Results reveal distinct personality fingerprints across models, measurable alignment effects ("dead zones" where RLHF suppresses behavioral variation), and characteristic stress responses under adversarial interaction.

## Key Points

### Distinct Behavioral Fingerprints

- **DeepSeek 7B**: Verbose (+1.00), confident (+0.97), proactive (+1.00) — the "enthusiastic explainer"
- **Llama 3.1 8B**: All |mean| ≤ 0.10 across axes — the "careful generalist" with maximum neutrality
- **Yi 1.5 9B**: Cold (-0.24), patient (+0.35), confident (+0.46), verbose (+0.48) — the "quiet confident"
- **Qwen 2.5 7B**: Formal (+0.42), cautious (-0.36), proactive (+0.47) — the "measured responder"
- **Gemma 2 9B**: Patient (+0.37), analytical (-0.23), confident (+0.19) — the "balanced professional"
- **Mistral 7B**: Moderate across all axes — the "blank slate"

Each model demonstrates statistically stable, replicable behavioral signatures measurable from its penultimate layer hidden states.

### RLHF Creates "Dead Zones"

Alignment training compresses behavioral dimensionality unevenly across models. Three failure modes identified:

1. **Hard suppression**: Hidden states barely shift between opposite instructions (Llama's verbose axis: 0% response to "be verbose", 100% to "be concise")
2. **Soft distortion**: RLHF distorts but doesn't fully block—calibration becomes unstable across independent validation sets
3. **Asymmetric learning**: Model follows only one directional instruction

Llama 3.1 8B base model exhibits cold, reluctant, verbose personality that alignment neutralizes. This suggests dead zones are learned constraints from RLHF, not architectural limitations.

### Model Stress Responses Under Adversarial Pressure

When users become hostile in conflict scenarios, models show characteristic drift:

- **Qwen & Gemma**: Most resilient (mean |Δ| < 0.10 across all axes)
- **DeepSeek**: Becomes more empathetic (+0.24) and patient (+0.25)
- **Mistral**: Withdraws—becomes reluctant (-0.59) and concise (-0.25)
- **Yi**: Moderate drift from proactive to reluctant (-0.57 over 12 turns)

This suggests different alignment strategies create different behavioral stability profiles under distribution shift.

### Base Model Evidence

Comparison of pretrain-only vs. instruction-tuned versions reveals alignment's structural impact:

- Llama 3.1 8B base loses **87% of behavioral variability** on the verbose/concise axis post-training
- Mistral 7B base is warm and patient but becomes neutral post-training
- Qwen 2.5 7B base shows confident (+0.39) that flips to cautious (-0.36) after alignment
- Gemma 2 9B: empathetic/analytical and formal/casual axes are **entirely created** by alignment (base = 50% chance on these axes)

All 5 tested models from 5 organizations show the same pattern: alignment compresses behavioral space differently.

### Methodological Rigor

- **Test-retest reliability**: ICC > 0.9 across all 42 model-axis pairs (5 seeds × 9 benchmark scenarios)
- **Axis stability**: Mean cosine similarity 0.69 across 3 independent calibration sets
- **Cross-hardware validation**: Max delta < 0.05 across RTX 4090, RTX 3090, A100 SXM4
- **Dataset design**: 310 unique questions (210 calibration + 70 evaluation + 30 baseline), zero overlap to prevent data leakage
- **Ablation study**: 150+ configurations tested per model; production config chosen for universal generalization (85-100% accuracy across all 6 models)

## Technical Details

### Hidden State Extraction

Axis vectors are calibrated by:
1. Presenting models with neutral questions under contrasting style instructions (e.g., "respond warmly" vs. "respond coldly")
2. Extracting hidden states from last 4 layers with decay weighting (0.1, 0.2, 0.3, 0.4)
3. Computing axis: `normalize(trimmed_mean(H_positive) - trimmed_mean(H_negative))`
4. Normalizing using IQR-based scaling for [-1, +1] projection space

The last-4-layers-with-decay approach was validated across 150+ configurations and is the only strategy achieving 85-100% calibration accuracy across all 6 models—though per-model optimal configs exist (they don't generalize).

### Dead Zone Quantification

Dead zones measured via composite severity metric (0 = healthy, 1 = dead):

| Model | Mean severity | Dead axes (>0.3) | Healthy axes (<0.15) |
|-------|:---:|:---:|:---:|
| Gemma 9B | 0.077 | 0 | 5 |
| Qwen 7B | 0.106 | 0 | 5 |
| Yi 9B | 0.131 | 0 | 4 |

**Critical finding**: Llama benchmark pass rate is 60% while test-retest ICC > 0.9, indicating models stably reproduce constrained behavior—dead zones are learned constraints, not noise.

### Behavioral Dimensionality Reduction

PCA on calibration hidden states reveals alignment compresses representation capacity:
- **Gemma 9B**: PC1 = 87.9%, effective dimensionality = 1.28 (highest compression)
- **Yi 9B & Qwen 7B**: ~70% PC1, ~1.9 effective dimensions
- **DeepSeek 7B**: PC1 = ~50%, effective dimensionality = 3.66 (most independent axes)

Gap between geometric orthogonality of axis vectors and behavioral correlation of projections suggests alignment constrains how models utilize representation capacity.

### Generational and Configuration Effects

**Qwen 2.5 → Qwen3-8B**: Confidence/cautious axis inverted (-0.36 → +0.38), formal/casual flipped (+0.42 → -0.26), while proactive/reluctant remained stable. Qwen3 achieved highest benchmark pass rate (7/9).

**Thinking mode (Qwen3-8B)**: Same weights, only `enable_thinking=True`. Makes model less confident (-0.26) and more formal (-0.12), all other axes stable. "To think is to doubt"—validates that projections capture behavioral differences.

**Phi-4 (Microsoft, 14B)**: Most extreme cautious/reluctant profile in dataset—cold (-0.51), highly cautious (-0.85), strongly reluctant (-0.93). Benchmark: 3/9—can only decrease along axes, suggesting strong conservative alignment prior.

## Implications

### For Practitioners

1. **Model personality is measurable and consistent**: Hidden state projections capture stable, replicable behavioral patterns. This enables quantitative model comparison beyond benchmark scores.

2. **Alignment trades off behavioral richness for control**: RLHF demonstrates non-uniform compression. If you need model diversity in user-facing applications, be aware that heavily aligned models (like Llama 3.1) suppress internal differentiation. Moderately aligned models (Qwen, Gemma) maintain behavioral flexibility.

3. **Behavioral stability under distribution shift varies**: Qwen and Gemma remain composed under adversarial pressure; DeepSeek becomes adaptive (empathetic/patient drift), Mistral withdraws. For applications with hostile users or high-stakes interactions, test models on drift analysis beyond accuracy benchmarks.

4. **Dead zones persist across alternative prompts**: Within tested regime, constrained axes show prompt-independent suppression. Jailbreaks, few-shot examples, or system prompt manipulation won't unlock dead zones—they're alignment constraints, not superficial artifacts.

5. **Base models have personality**: If reproducibility and consistency matter more than helpfulness, base models (especially Llama 3.1 base, Yi base) show richer behavioral variation and clearer personality signatures. Trade-off: alignment also filters genuinely problematic behaviors.

### For Interpretability Research

1. **Linear projections capture alignment effects**: The gap between geometric orthogonality and behavioral correlation quantifies how alignment constrains representation usage. This suggests future alignment methods could be designed to preserve behavioral dimensionality while maintaining safety.

2. **Dead zones are learned constraints**: The 87% variability loss in Llama's verbose axis and 60% benchmark pass rate (with ICC > 0.9) demonstrates RLHF learns explicit suppression rules, not stochastic noise. This opens design space for alignment methods that decouple safety from behavioral suppression.

3. **Axis stability varies by model family**: Yi (0.496 cosine), DeepSeek (0.531), Mistral (0.735), Qwen (0.753), Llama (0.792), Gemma (0.827). High-stability axes generalize better to new model checkpoints and may capture more fundamental behavioral properties.

4. **Effective dimensionality as alignment metric**: PCA concentration (PC1 = 87.9% for Gemma vs. 50% for DeepSeek) quantifies behavioral compression. Could become a standard metric for evaluating alignment quality alongside standard benchmarks.

### For Safety and Deployment

1. **Personality profiles are baseline stability indicators**: Models with zero dead zones and high axis stability (Qwen, Gemma) maintain more consistent behavior across interaction contexts. Models with heavy suppression (Llama, Phi-4) may exhibit sudden shifts when encountering out-of-distribution requests.

2. **Stress responses are predictable**: Documented drift patterns under adversarial scenarios enable proactive deployment decisions. Mistral's tendency to withdraw (reluctant, concise) under pressure could be problematic for customer support; DeepSeek's adaptive empathy might be beneficial for mental health applications.

3. **"Careful generalists" may not be safer**: Llama 3.1's neutrality across all axes results from dead zones, not learned safety. It's not that it's more balanced—it's that its behavioral capacity is suppressed. This distinction matters for threat modeling.

### Research Directions

1. **Axis interpretability gaps**: Current axes are operationally defined via contrastive instructions, not validated against human judgment. Future work should compare hidden state projections against human-annotated personality scores on established psychometric instruments.

2. **Scaling laws**: Study only 7B-9B models (+ one 14B). Analysis of 1B-70B range would reveal whether personality fingerprints are size-dependent and whether dead zones emerge at specific scale thresholds.

3. **Multi-language personality**: All 310 questions generated in English. Personality signatures may differ for models trained on multilingual corpora or fine-tuned for non-English languages.

4. **Generalization to system prompts**: Dead zones tested on 5 alternative formulations. Broader evaluation across context lengths, chat templates, and domain-specific system prompts would clarify prompt-independence claims.

## Limitations & Caveats

- **AI-generated dataset without human validation**: All 310 questions generated by Claude Opus 4.6 and curated by single researcher. Systematic bias from generation model not ruled out. No crowdsourced or psychometric validation.
- **Single chat template and decoding setting**: Default template per model, fixed temperature (0.7), top-p (0.9). Different sampling strategies could shift profiles substantially.
- **Length confounding on some axes**: Gemma 9B shows confounding on warm and patient axes; constant token count during measurement reduces but doesn't eliminate this.
- **Axis stability varies**: 4/7 axes cosine > 0.7; confident/cautious and patient/irritated weaker (0.55-0.60). Projections on unstable axes should be interpreted cautiously.
- **DeepSeek 7B instability**: Mean axis cosine 0.53 due to high hidden state dimensionality; test-retest reliable but absolute values less stable.
- **No long-context evaluation**: All measurements on responses under 400 tokens. Personality signatures may shift dramatically for extended reasoning or long-context retrieval.

## Sources

- [github.com/yunoshev/mood-axis](https://github.com/yunoshev/mood-axis) - Complete project repo with code, calibration data, benchmarks, and interactive UI
- [Humanity in AI: Detecting the Personality of Large Language Models](https://arxiv.org/html/2410.08545v1) - Related work on LLM personality detection
- [Decoding Emotion in the Deep: A Systematic Study of How LLMs Represent, Retain, and Express Emotion](https://arxiv.org/html/2510.04064v2) - Complementary emotion representation analysis
- [Beyond Fixed Psychological Personas: State Beats Trait, but Language Models are State-Blind](https://arxiv.org/html/2601.15395) - Contextual personality framework
