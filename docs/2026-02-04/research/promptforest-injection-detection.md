# PromptForest: Ensemble-Based Prompt Injection Detection for LLMs

| | |
|---|---|
| **Source** | r/MachineLearning + Hacker News |
| **URL** | [old.reddit.com/r/MachineLearning/comments/1qvkh6m](https://old.reddit.com/r/MachineLearning/comments/1qvkh6m/p_i_built_an_opensource_ensemble_for_fast/) |
| **Researched** | 2026-02-04 |

## Overview

PromptForest is an open-source, lightweight ensemble system for detecting prompt injection attacks in LLM-based applications. Rather than relying on single large classifiers, it aggregates predictions from multiple specialized detectors using weighted voting with discrepancy scoring. The approach achieves competitive accuracy with significantly lower latency (~100ms/request) and better-calibrated confidence scores—critical for production safety systems where overconfidence on edge cases can be catastrophic.

## Key Points

- **Ensemble over monolithic**: Combines 3 lightweight detectors (Llama Prompt Guard 86M, Vijil Dome ModernBERT, PromptForest-XGB) instead of single large classifiers
- **Parameter efficiency**: ~237M total parameters vs ~600M for leading baselines (60% smaller footprint)
- **Calibration focus**: Lower Expected Calibration Error (ECE) means the model expresses appropriate uncertainty, triggering human-in-the-loop fallbacks when uncertain rather than silently failing
- **Production-ready latency**: 100ms per request enables real-time filtering in API pipelines without throughput penalties
- **Model selection via benchmarking**: Only includes detectors that improve ensemble performance; explicitly removed components (e.g., ProtectAI's DeBERTa finetune) that degraded calibration
- **Discrepancy scoring**: Disagreement between ensemble members flags ambiguous cases for stricter handling or human review

## Technical Details

### Architecture

The ensemble uses weighted voting where predictions are aggregated based on each model's individual accuracy on validation data. This specialization approach lets models contribute more on cases where they excel and less where they struggle, achieving better overall coverage than uniform voting.

Key insight from the Reddit post: not all models are equally good at every injection pattern. By benchmarking each candidate model and removing poor performers, the ensemble becomes smaller and more coherent than simply throwing everything together.

### Performance Characteristics

- **Speed**: ~100ms inference vs considerably slower monolithic BERT classifiers
- **Calibration**: Lower confidence on incorrect predictions reduces false-positive confidence and enables safer fallback strategies
- **Robustness**: Voting mechanism adds redundancy; single model failures don't cause system-wide degradation
- **Deployment flexibility**: Python-first with CLI and server mode for easy integration into existing pipelines

### Supported Detection Backends

The system is designed as extensible—it can incorporate various detection models:
- Meta Llama Prompt Guard variants (86M-class)
- ModernBERT finetuned detectors (Vijil Dome)
- Custom XGBoost classifiers on embedding features
- Room for additional detectors (TensorFlow, custom fine-tuned models)

## Implications

### Architecture Decision: Ensemble over Scale

This challenges the conventional wisdom that bigger single models solve security problems. Instead, PromptForest demonstrates that for detection tasks, smaller ensemble components with proper weighting and calibration can outperform larger monoliths on the metrics that actually matter in production (latency, confidence reliability, inference cost).

The calibration emphasis is particularly significant: most deployed detectors are overconfident, meaning they fail silently on adversarial edge cases. An ensemble that admits uncertainty is fundamentally safer than a single large model that sounds confident but is wrong.

### Integration Patterns

As LLM use cases move into user-generated-content pipelines and autonomous agent systems, injection detection becomes not an afterthought but a foundational layer. PromptForest's latency profile (100ms) makes it practical to:
- Pre-filter all user inputs before reaching the main model
- Run as a validation step in agentic loops
- Operate in resource-constrained environments (edge, serverless)

### Limitations and Scope

The project explicitly notes it is not a standalone security solution. Best practice remains defense-in-depth:
- Input sanitization (NeMo Guardrails, schema enforcement)
- System prompting (constitution, role definition, capability boundaries)
- Detection ensemble (PromptForest)
- Post-generation filtering / output validation

Each layer catches different attack patterns. Detection alone without input sanitization leaves pathways open.

### Open-Source Alignment

Published as open-source with CLI and Colab notebook, removing barriers for researchers and practitioners. This contrasts with proprietary detection APIs that create lock-in and lack transparency. Community-driven improvements to detection methods benefit the entire ecosystem.

## Sources

- [r/MachineLearning/comments/1qvkh6m - PromptForest Reddit Post](https://old.reddit.com/r/MachineLearning/comments/1qvkh6m/p_i_built_an_opensource_ensemble_for_fast/) - Original project announcement with architecture rationale
- [GitHub - appleroll-research/promptforest](https://github.com/appleroll-research/promptforest) - Source code, documentation, and implementation details
- [Hacker News - PromptForest Discussion](https://news.ycombinator.com/item?id=46792576) - Creator commentary on design philosophy and use cases
