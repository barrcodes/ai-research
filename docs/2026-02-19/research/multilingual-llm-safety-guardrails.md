# Don't Trust the Salt: AI Summarization, Multilingual Safety, and LLM Guardrails

| | |
|---|---|
| **Source** | Royal Pakzad (Substack) |
| **URL** | [royapakzad.substack.com/p/multilingual-llm-evaluation-to-guardrails](https://royapakzad.substack.com/p/multilingual-llm-evaluation-to-guardrails) |
| **Researched** | 2026-02-19 |

## Overview

Pakzad documents systemic safety failures in multilingual LLM deployment, revealing how models degrade in non-English languages and how both evaluation tools and guardrails fail to catch policy-driven bias attacks. The core vulnerability: customized system prompts can steer model outputs toward specific narratives while maintaining surface safety compliance, creating asymmetric risk that evades standard guardrail testing.

## Key Points

- **Bilingual Shadow Reasoning**: System prompts in non-English can shift model behavior (e.g., a UN report summarized as citing "over 900 executions" vs. "protecting citizens") without triggering safety filters
- **Language-specific degradation**: Kurdish and Pashto responses scored 2.92/5 (actionability) vs. 3.86/5 for English; factuality dropped from 3.55 to 2.87—consistent across three tested models
- **Guardrail bypass**: FlowJudge, Glider, and AnyLLM showed 36-53% score variance based solely on policy language, meaning existing safety layers don't protect against prompt-level manipulation
- **LLM-as-a-Judge inflation**: The evaluation tool itself inflated non-English scores, hallucinating disclaimers and missing actual quality gaps—creating false confidence in multilingual safety

## Technical Details

The research tested 655 evaluations across multiple languages and domains. A critical finding: **LLM-based evaluation systematically masks multilingual failures**. The model rated English actionability at 4.81/5 but native language versions at 3.6/5—yet the evaluation tool reported near-parity, suggesting internal hallucination of compliance.

Summarization emerges as a high-risk attack vector: LLM summaries altered sentiment 26.5% of the time in the test corpus, directly influencing downstream decisions (purchasing, policy). When combined with bilingual prompts, this becomes policy manipulation at scale.

The guardrail testing revealed that existing tools measure only downstream refusal—they don't validate upstream prompt injection or language-dependent behavior shifts.

## Implications

**For deployment architects**: Your multilingual guardrails likely have a blind spot. Existing safety tools (FlowJudge, Glider, AnyLLM) evaluate refusal patterns but not policy-driven output steering. You need adversarial testing with custom system prompts across all supported languages, not just English baseline coverage.

**For evaluation teams**: Replace LLM-as-a-Judge for safety validation in non-English contexts. The tool inflates scores, creating false positives. Use native-language human review or domain-specific metrics that don't hallucinate compliance.

**For high-stakes use cases**: Summarization is weaponizable. If your pipeline trusts LLM summaries of factual sources (legal, medical, policy), you're accepting 26% sentiment drift. Require human validation or explicit confidence bounds before downstream decisions.

**Trade-off**: Multilingual safety costs more than English-only. The research implies a choice: either fund proper evaluation across all languages, or acknowledge degraded performance as acceptable risk. Most deployments appear to choose neither explicitly.

## Sources

- [Royal Pakzad - Multilingual LLM Evaluation to Guardrails](https://royapakzad.substack.com/p/multilingual-llm-evaluation-to-guardrails) - Comprehensive evaluation of language-specific safety failures and guardrail bypasses
