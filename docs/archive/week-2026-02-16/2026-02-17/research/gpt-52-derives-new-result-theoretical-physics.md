# GPT-5.2 Derives a New Result in Theoretical Physics

| | |
|---|---|
| **Source** | OpenAI |
| **URL** | [openai.com/index/new-result-theoretical-physics/](https://openai.com/index/new-result-theoretical-physics/) |
| **Researched** | 2026-02-17 |

## Overview

GPT-5.2 has conjectured a new formula for gluon scattering amplitudes in theoretical physics, subsequently verified by both an internal scaffolded model and mathematical proof. The result challenges textbook assumptions about particle interactions that were previously thought to be zero, opening new inquiry directions in high-energy physics. This work demonstrates a concrete pattern-recognition capability of modern LLMs: reducing complex mathematical expressions to simple, generalizable formulas that hint at deeper physical structure.

## Key Points

- **Novel Physics Result**: GPT-5.2 identified a nonzero scattering amplitude for a specific gluon configuration ("single-minus" helicity) that standard physics arguments had marked as impossible, specifically in the "half-collinear regime" where particle momenta follow special alignment conditions.

- **AI-Assisted Research Workflow**: The methodology showcases a scalable pattern: humans computed base cases (n up to 6) by hand producing superexponentially complex Feynman diagram expansions; GPT-5.2 Pro simplified these expressions, spotted patterns, and posited a general formula; a scaffolded GPT-5.2 spent 12 hours proving its validity.

- **Rigorous Verification**: The formula was validated against two independent constraints—the Berends-Giele recursion relation and soft theorems—establishing it as mathematically sound, not mere conjecture.

- **Broader Implications**: Work already extends to gravitons (gravitational force carriers), signaling this is a systematic discovery with cascading applications in scattering amplitude theory.

## Technical Details

**The Problem Context**: Scattering amplitudes compute interaction probabilities in quantum field theory. Gluon amplitudes at tree-level (no quantum loops) exhibit unexpected simplifications that reveal deeper symmetries. One configuration—all gluons positive helicity except one negative—had been dismissed as impossible under standard generic-momentum assumptions.

**The Breakthrough**: The authors discovered that this amplitude becomes nonzero in a precisely defined kinematic subspace: the "half-collinear regime" where particle momenta obey strict alignment constraints. While non-generic, this regime is mathematically well-defined and physically relevant.

**Formula Derivation**:
- Manual calculation up to n=6 yielded expressions (Eqs. 29–32) with Feynman complexity growing superexponentially
- GPT-5.2 Pro reduced these to compact forms (Eqs. 35–38)
- Pattern recognition yielded closed-form Eq. (39) for all n
- Scaffolded GPT-5.2 proved the formula valid
- Cross-validated against Berends-Giele recursion and soft-limit constraints

**Human-AI Coupling**: The paper exemplifies distributed cognition—humans define problems and verify results; LLMs handle pattern detection, formula simplification, and proof generation at scales humans cannot easily manage.

## Implications

**For Theoretical Physics**: This demonstrates that computational bottlenecks in deriving scattering amplitude formulas—historically a fiddly, manual task—are now tractable via AI-assisted exploration. Physicists gain a tool for systematic formula discovery that may uncover hidden structures in QFT.

**For AI-Assisted Science**: The work validates a template: LLMs + domain experts + rigorous verification = publishable frontier physics. This challenges the narrative that LLMs are purely extractive tools; they can operate as creative mathematical engines when coupled with human validation frameworks.

**For Architecture of Scientific Practice**: Illustrates a hybrid workflow where AI strengths (pattern matching at scale, expression simplification) complement human expertise (problem formulation, physical intuition, proof strategy). This pattern likely generalizes across mathematics, optimization, and theoretical modeling disciplines.

**For Engineering Teams**: Signals that scaffolding and multi-step reasoning (12 hours of proof-search) are justified investments for high-value discovery tasks. The cost of compute for verification is negligible against the value of validated novel results.

## Sources

- [arXiv Preprint: Single-minus gluon tree amplitudes are nonzero](https://arxiv.org/abs/2602.12176) - Full mathematical paper by Guevara, Lupsasca, Skinner, Strominger, and Weil
- [OpenAI Research Index](https://openai.com/research/index/) - OpenAI's research publications
