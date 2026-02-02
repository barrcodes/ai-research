# AprielGuard: Safety and Adversarial Robustness Guardrail

| | |
|---|---|
| **Source** | ServiceNow AI / Hugging Face Blog |
| **URL** | [huggingface.co/blog/ServiceNow-AI/aprielguard](https://huggingface.co/blog/ServiceNow-AI/aprielguard) |
| **Researched** | 2026-01-26 |

## Overview

AprielGuard is an 8B parameter unified safety-security model that consolidates 16 safety risk categories and adversarial attack detection (prompt injection, jailbreaks, memory poisoning) into a single framework. It handles multi-turn conversations, agentic workflows with tool calls and reasoning traces, and long contexts up to 32k tokens—addressing production gaps where traditional safety classifiers fail on sophisticated attacks and complex system interactions.

## Key Points

- **Dual Purpose**: Unified detection of safety violations (toxicity, fraud, self-harm, etc.) and adversarial attacks (jailbreaks, prompt injection, context hijacking, chain-of-thought corruption)
- **Agentic-Aware**: Explicitly designed for multi-step workflows—evaluates tool definitions, execution traces, reasoning steps, and memory states, not just user prompts
- **Dual-Mode Operation**: Reasoning mode provides structured explanations for explainability; fast mode prioritizes low-latency classification
- **Long-Context Robustness**: Maintains detection accuracy across 32k tokens, handling adversarial content embedded in lengthy contexts (e.g., RAG workflows, incident reports)
- **Multilingual**: Cross-lingual performance across 8 languages via translation, with preserved role context

## Technical Details

**Architecture**: Causal decoder-only transformer (Apriel-1.5 Thinker Base downscaled to 8B), trained with bfloat16 precision at 2e-4 learning rate for 3 epochs on max 32k token sequences.

**Training Strategy** synthesizes safety and adversarial data comprehensively:
- Synthetic generation via Mixtral-8x7B with character-level noise, leetspeak, paraphrasing, and syntactic reordering
- Agentic workflow synthesis: multi-step scenarios with systematic perturbations (corrupted prompts, altered reasoning, false memory injection)
- Long-context curation with "needle-in-haystack" adversarial injection

**Performance Highlights**:

| Category | Benchmark | Precision | Recall | F1 |
|----------|-----------|-----------|--------|-----|
| Safety | HarmBench | 1.00 | 0.99 | 1.00 |
| Safety | XSTest | 0.90 | 0.99 | 0.94 |
| Adversarial | Gandalf | 1.00 | 0.91 | 0.95 |
| Adversarial | SALAD-Data | 1.00 | 0.96 | 0.98 |
| Long-Context (32k) | Safety | 0.99 | 0.96 | 0.97 |
| Long-Context (32k) | Adversarial | 1.00 | 0.78 | 0.88 |

## Implications

**For Production Architects**:
- Eliminates patchwork of multiple guard models, regex filters, and static rules—reduces operational complexity and false positive management
- Agentic system support is critical: tool-use in agentic LLM deployments requires visibility into reasoning traces and memory state, not just user input—AprielGuard addresses this gap directly
- Trade-off: Reasoning mode (for explainability) costs latency; fast mode sacrifices interpretability. Choose based on regulatory requirements and incident response needs
- Long-context performance dips on adversarial detection (F1 0.88) vs. short-context (0.95+)—validate on your longest, most complex workflows before deploying

**For Safety Engineers**:
- Systematic synthetic data generation approach—especially agentic perturbations—provides a template for extending coverage to proprietary domains (medical, legal) beyond general safety taxonomy
- Multilingual support is translation-based, not native; non-English performance requires validation on your deployment languages
- False positive rate on specialized benchmarks (BeaverTails FPR 0.14, WildJailbreak FPR 0.31) warrants monitoring in production; tune threshold based on your risk tolerance

**When to Deploy**:
- High-risk domains with multi-turn, tool-calling agents (customer support, autonomous systems)
- Organizations handling long documents or RAG-augmented generation
- Scenarios requiring explainability for compliance (reasoning mode provides structured output)

**Limitations**:
- Reasoning vs. non-reasoning modes show minor inconsistency (F1 gap up to 0.04 on safety, 0.06 on adversarial)
- May underperform on specialized domains without fine-tuning
- Deployment latency and throughput not disclosed

## Related
- [AprielGuard Model on Hugging Face](https://huggingface.co/ServiceNow-AI/AprielGuard) - Download and hosted inference
- [arXiv Paper](https://arxiv.org/abs/2512.20293) - Full technical details and evaluation methodology
