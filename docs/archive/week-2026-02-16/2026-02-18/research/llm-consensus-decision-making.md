# Using LLM Consensus to Remove Flaws in Decision Making

| | |
|---|---|
| **Source** | Reddit r/artificial |
| **URL** | [old.reddit.com/r/artificial/comments/1r8070j/](https://old.reddit.com/r/artificial/comments/1r8070j/using_combine_consensus_of_llms_to_remove_or/) |
| **Researched** | 2026-02-18 |

## Overview

A practical framework for mitigating LLM weaknesses through structured adversarial debate protocols rather than waiting for better models. The approach uses multiple LLMs across different vendors to validate reasoning, expose hallucinations, and enforce accountability through formalized argumentation structures.

## Key Points

- **Problem Statement**: Individual LLMs suffer from well-known flaws (hallucination, hedging, confabulation, poor anchoring) that can't always be resolved through prompting alone. Current practice relies on either human expertise validation or hoping for better models.

- **Empirical Validation Method**: Cross-LLM consensus where one LLM critiques another's output significantly improves decision quality. This works even when human practitioners lack domain expertise—the LLMs themselves validate each other's reasoning chains.

- **Adversarial Debate Protocol**: Implements structured argumentation rules borrowed from formal debate to constrain LLM outputs. Models must defend positions with evidence, expose their reasoning, and respond to counterarguments rather than simply generating outputs unconstrained.

- **Vendor Agnostic**: Works across different LLM providers, suggesting the approach addresses fundamental reasoning gaps rather than model-specific quirks. This heterogeneity itself becomes a feature—reduces single-model cognitive biases.

- **Cumulative Validation**: Combines "cumulative wisdom" from multiple perspectives with "adversarial consensus"—not pure consensus (which can amplify shared errors) but forced engagement with contradictions.

## Technical Details

The protocol operates through a GitHub-maintained Claude code skill with three documented components:

1. **PROTOCOL-EXPLAINED-FOR-HUMANS.md**: Documents how adversarial debate rules force LLMs toward responsible outputs by making them accountable for quality.

2. **SCIENTIFIC.md**: Provides theoretical grounding for why this approach works (likely touching on debate theory and multi-agent reasoning systems).

3. **PROTOCOL-FLOW-DIAGRAMS.md**: Shows implementation patterns for orchestrating multi-LLM interaction flows.

The implementation enables:
- Explicit reasoning disclosure (models show their thinking, not just conclusions)
- Proof-of-work enforcement (backing claims with evidence chains)
- Point-by-point counter-argumentation (forcing engagement with disagreement)
- Prevention of confabulation through verification loops (original LLM must defend choices when challenged)

## Implications

**For architecture**: This patterns suggests consensus/validation layers are viable system components deployable today without waiting for model improvements. Multi-LLM routing strategies can encode domain-specific verification without external oracles.

**For reliability**: In domains where you can't personally validate outputs, consensus protocols provide a structural mechanism for LLM-to-LLM validation. This is architecturally different from (and likely more cost-effective than) human review loops.

**For agent design**: The debate structure hints at how to build agentic systems where specialized LLMs with different orientations can challenge each other's conclusions before returning results. This moves beyond simple majority voting toward deeper reasoning validation.

**Limitations**: The approach requires orchestration overhead and multiple inference calls. Success depends on the LLMs actually engaging substantively with disagreements rather than conforming to each other. No discussion of failure modes (e.g., what happens when all LLMs converge on a wrong answer due to training data overlap).

## Sources

- [LLM Argumentation Protocol](https://github.com/Alex-R-A/llm-argumentation-protocol) - Main implementation repo with protocol documentation and flow diagrams
- [Protocol Explained For Humans](https://github.com/Alex-R-A/llm-argumentation-protocol/blob/main/PROTOCOL-EXPLAINED-FOR-HUMANS.md) - Practical guide to adversarial debate structures
- [Scientific Foundation](https://github.com/Alex-R-A/llm-argumentation-protocol/blob/main/SCIENTIFIC.md) - Theoretical framework
- [Protocol Flow Diagrams](https://github.com/Alex-R-A/llm-argumentation-protocol/blob/main/PROTOCOL-FLOW-DIAGRAMS.md) - Implementation patterns
