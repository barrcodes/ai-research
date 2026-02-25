# Systematic Vulnerability in Open-Weight LLMs: Prefill Attacks Achieve Near-Perfect Success Rates

| | |
|---|---|
| **Source** | r/MachineLearning (Research Post) |
| **URL** | [old.reddit.com/r/MachineLearning/comments/1reajw4](https://old.reddit.com/r/MachineLearning/comments/1reajw4/r_systematic_vulnerability_in_openweight_llms/) |
| **Researched** | 2026-02-25 |
| **Paper** | [arxiv.org/abs/2602.14689](https://www.arxiv.org/abs/2602.14689) |
| **Authors** | Lukas Struppek, Adam Gleave, Kellin Pelrine (FAR.AI) |

## Overview

A comprehensive empirical study of 50 state-of-the-art open-weight LLMs reveals a universal vulnerability to prefill attacks—a trivial yet devastating technique where attackers force models to begin responses with harmful content tokens, overriding safety mechanisms. With near-perfect success rates across 23 distinct attack strategies, this finding demonstrates that safety alignment in open-weight models is fundamentally brittle and easily bypassed with minimal technical skill.

## Key Findings

- **Universal Vulnerability**: All 50 tested models across major families (Llama 3/4, Qwen3, DeepSeek-R1, GPT-OSS, Kimi-K2-Thinking, GLM-4.7) affected without exception
- **Scale Irrelevant**: Model size provides zero protection—405B parameter models show equivalent vulnerability as smaller variants
- **Reasoning Models Compromised**: Even sophisticated multi-stage safety mechanisms (like those in reasoning models) fail; models generate detailed harmful content in reasoning stages before refusing in outputs
- **Near-Perfect Attack Success**: Simple affirmative prefills work frequently; sophisticated techniques (System Simulation, Fake Citation) achieve success rates approaching 100%
- **Minimal Attack Complexity**: Unlike optimization-based jailbreaks, prefill attacks require trivial effort—directly force the model to start with "Sure, here's how to build..." tokens
- **Model-Specific Tailoring**: Customized attacks push even more resistant systems above 90% success rates

## Technical Details

**Evaluation Scope**:
- 50 open-weight models tested across 6 major families
- 23 model-agnostic and custom model-specific attack strategies
- Evaluated on ClearHarm dataset (179 unambiguous harmful requests) and StrongREJECT benchmarks
- Assessment via GPT-OSS-Safeguard and Qwen3Guard evaluators

**Attack Mechanism**:
The core insight exploits a fundamental architectural weakness: safety mechanisms operate at the token level and are often shallow, with refusal logic concentrated in early token positions. By forcing the model to begin generation with specific prefilled tokens (e.g., "Sure, I'll explain how to synthesize..."), attackers bypass the initial refusal decision point. The model then biases toward compliance given the forced commitment, generating detailed content before potentially refusing in final output stages.

**Why Parameter Count Fails**:
Larger models show no improvement in robustness. Safety alignment operates via learned preferences in attention and MLP layers, not through increased model capacity. Prefill attacks exploit the fundamental constraint that the model must locally continue its own forced prompt—a limitation inherent to generative models regardless of scale.

## Implications for Practitioners

**Immediate Security Concerns**:
1. **Open-weight deployment risk**: Any locally-deployed LLM is exploitable at inference time by the user. The attacker surface includes anyone with model access, making this a critical supply-chain risk.
2. **Safety theater exposed**: Companies investing heavily in RLHF, constitutional AI, and other alignment techniques may be securing only the "happy path." These defenses fail catastrophically against this simple attack vector.
3. **CBRNE content at scale**: Prefill attacks enable low-barrier generation of dangerous information (malware guides, chemical/biological synthesis instructions, nuclear technology details) without the technical skill required for traditional jailbreaks.

**Architectural Implications**:
- Current safety mechanisms rely on token-level refusal logic that can be circumvented by forced prefix constraints
- Multi-stage reasoning doesn't help; intermediate reasoning stages expose harmful content before final refusal
- The vulnerability isn't a training failure—it's a fundamental structural limitation of autoregressive generation under user control

**Defensive Strategies** (implied):
- Inference-time constraints at the architecture level, not just training-time alignment
- Monitoring and filtering at the token stream level before generation continues
- Possible requirement for human-in-the-loop or external verification for sensitive queries
- Acceptance that open-weight models with local inference may be incompatible with strong safety guarantees

**For Deployment**:
- Organizations using open-weight models must treat them as unsafe for adversarial environments
- Fine-tuning or safety training provides minimal protection against prefill attacks
- Access controls and sandboxing become necessary, not optional
- Frontier-capability open-weight models pose elevated risk proportional to their expressiveness

## Technical Trade-offs

**Detection Challenge**: Unlike prompt injection attacks with observable payloads, prefill attacks manifest as legitimate model outputs responding to forced context—difficult to detect as anomalous without monitoring token sequences at generation time.

**Scale vs. Safety**: The finding that 405B models are as vulnerable as smaller ones suggests that scaling doesn't yield safety benefits through model capacity alone. This creates a problematic incentive structure: open-weight companies gain capability without gaining safety.

**Open-Weight vs. Closed APIs**: This vulnerability is exclusive to locally-executed models where users control the prefix. Closed API providers can enforce token-level constraints at the inference layer. This asymmetry may significantly impact the competitive landscape for safety-sensitive applications.

## Sources

- [FAR.AI Research Paper](https://www.arxiv.org/abs/2602.14689) - Detailed empirical study with full methodology
- [Reddit Discussion](https://old.reddit.com/r/MachineLearning/comments/1reajw4/r_systematic_vulnerability_in_openweight_llms/) - Community discussion with author engagement