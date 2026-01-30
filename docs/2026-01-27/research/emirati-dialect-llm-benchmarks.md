# Alyah: Toward Robust Evaluation of Emirati Dialect Capabilities in Arabic LLMs

| | |
|---|---|
| **Source** | HuggingFace Blog (TIIUAE) |
| **URL** | [huggingface.co/blog/tiiuae/emirati-benchmarks](https://huggingface.co/blog/tiiuae/emirati-benchmarks) |
| **Researched** | 2026-01-27 |

## Overview

TIIUAE introduces Alyah, a specialized 1,173-sample benchmark for evaluating Arabic LLMs on Emirati dialect understanding—addressing a critical blind spot in existing Arabic NLP evaluation. Rather than relying on Modern Standard Arabic (MSA) metrics, the benchmark assesses dialectal semantics, cultural knowledge, and authentic linguistic expressions through seven distinct categories with varying difficulty levels.

## Key Points

- **Gap in evaluation**: Existing Arabic benchmarks focus on MSA while ignoring regional dialects, creating false confidence in model capabilities for end users
- **Multi-dimensional assessment**: Seven categories (Language & Dialect, Etiquette & Values, Imagery, Religious/Social Sensitivity, Heritage Knowledge, Poetry, Greetings) reveal uneven competence across dialectal dimensions
- **Instruction-tuning impact**: Instruction-tuned models significantly outperform base models (82% vs 74% top scores), suggesting fine-tuning improves cultural appropriateness
- **Persistent challenges**: Even top models achieve only 40-50% accuracy on core dialect and daily expressions—indicating deep semantic gaps beyond surface vocabulary
- **Open dataset + integrated evaluation**: Benchmark available on HuggingFace with lighteval integration, designed as diagnostic tool for guiding future training data collection

## Technical Details

| Component | Details |
|-----------|---------|
| **Dataset Size** | 1,173 manually curated samples from native speakers |
| **Format** | Multiple-choice, 4 options per question with randomized answer positions |
| **Quality Control** | Human review of synthetically-generated distractors; semantic correctness assessment |
| **Models Tested** | 54 total: Arabic-native (Jais, Allam), multilingual (Qwen, LLaMA 3.1), regionally-adapted (Fanar, AceGPT) |
| **Top Base Model** | google/gemma-3-27b-pt (74.68%) |
| **Top Instruction-Tuned** | falcon-h1-arabic-7b-instruct (82.18%) |

**Critical Finding**: Performance variance across categories demonstrates models lack **unified dialectal competence**—they excel in isolated categories while struggling in core linguistic competence. Language & Dialect category represents the hardest benchmark even for specialized Arabic models.

## Implications

### For Model Development
Dialect-specific training cannot be bolted on through generic multilingual pre-training. Models require targeted dialect-specific data or fine-tuning phases to bridge the 30-40% accuracy gaps observed in core categories.

### For Evaluation Strategy
Moving beyond aggregate accuracy: single benchmark scores obscure critical gaps. Multi-category breakdown reveals whether models understand *cultural context* (Etiquette at 70%+) while failing at *everyday expressions* (Greetings at 60-70%).

### Architectural Decision Points
- **Instruction-tuning ROI**: Substantial performance gains justify adding fine-tuning stages targeting dialect-specific SFT data
- **Data sourcing**: Benchmark methodology (native speaker curation + distractor generation) establishes replicable pattern for other dialects (Egyptian, Levantine, Gulf variants)
- **Regional model specialization**: Results validate separate model tracks for high-context dialects rather than attempting unified Arabic models

### Practical Application
Organizations deploying Arabic LLMs for Gulf/UAE contexts should treat MSA benchmarks as necessary but insufficient validation. Alyah provides actionable diagnostic data: if a model scores <70% on Etiquette & Values, cultural appropriateness risks emerge in customer-facing applications.

## Related

- [TIIUAE Falcon-H1 Models](https://huggingface.co/tiiuae) - Instruction-tuned variants showing 8-10% accuracy improvements on Alyah
- [Alyah Dataset](https://huggingface.co/datasets/tiiuae/alyah-emirati-benchmark) - Full benchmark available for research and model evaluation
- [Lighteval Integration PR](https://github.com/huggingface/lighteval/pull/1117) - Standardized evaluation harness for reproducible testing
