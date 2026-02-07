# AxiomProver Solved Fel's Open Conjecture

| | |
|---|---|
| **Source** | arXiv + Reddit discussion (r/singularity) |
| **URL** | [arxiv.org/html/2602.03716v1](https://arxiv.org/html/2602.03716v1) |
| **Researched** | 2026-02-07 |

## Overview

AxiomProver autonomously solved Fel's conjecture on syzygies of numerical semigroups—an unsolved research-level mathematics problem—generating a complete formal proof in Lean 4 with zero human guidance. This marks the first time an AI system has settled an open conjecture in theory-building mathematics, demonstrating viability for end-to-end formalization of research-grade problems from natural language specifications.

## Key Points

- **Problem**: Fel's conjecture provides an explicit formula for normalized alternating syzygy power sums in numerical semigroups, connecting gap invariants to universal symmetric polynomials.
- **Solution approach**: The system autonomously generated both formal problem statement and proof by leveraging exponential generating functions (EGFs) as the central algebraic strategy.
- **Zero human input**: Given only a LaTeX problem statement and configuration (Lean 4.26.0), AxiomProver produced verification-passing Lean code without intermediary human formalization effort.
- **Significance**: First demonstration that AI systems can move beyond competition mathematics (where AxiomProver previously scored perfectly on Putnam exams) to tackle legitimate unsolved research conjectures.

## Technical Details

**Proof Strategy:**
The solution converts syzygy degrees and gap information into exponential generating function form, defining A(t) and B(t) generating functions for universal polynomials. The proof extracts and compares coefficients of t^(m+p) across these functions, isolating the universal identities required for the formula derivation. This coefficient-extraction approach avoided extensive formalization of numerical semigroup machinery—a key architectural advantage.

**Input/Output Flow:**
- **Input**: Natural-language LaTeX problem statement with self-contained definitions
- **Process**: End-to-end formalization pipeline
- **Output**: Lean 4 proof verified by the kernel (problem.lean + solution.lean)

**Why this matters architecturally**: The system doesn't require problem decomposition, intermediate human interpretation, or detailed specifications. It processes research-level mathematical statements directly into machine-verifiable proofs—a substantial step forward from supervised theorem proving on known problems.

## Implications

**For mathematical research**: This demonstrates AI can operate in the theory-building phase of mathematics, not just verification. Future systems may enable researchers to rapidly formalize conjectures and explore solution strategies at scale.

**For AI reasoning systems**: Moving from competition mathematics (bounded problems with known solutions) to open research problems is a meaningful capability transition. It requires stronger abstraction, hypothesis generation, and strategic proof synthesis rather than pattern matching against known problem types.

**Practical next steps**: The approach works well for algebraic/combinatorial domains amenable to generating function techniques. Extensions to topology, analysis, or domains requiring numerical methods remain open questions. The system's reliance on problem self-containment suggests scalability challenges for problems requiring extensive background theory.

**Trade-offs**: AxiomProver's success on this conjecture doesn't generalize to all unsolved problems. Selection of theory-building, formula-oriented domains (syzygies, generating functions) played a crucial role. Problems requiring deep insight from problem history or intuition-guided exploration may resist similar approaches.

## Sources

- [arxiv.org/abs/2602.03716](https://arxiv.org/abs/2602.03716) - Fel's Conjecture on Syzygies of Numerical Semigroups (full paper)
- [x.com/axiommathai/status/2019449659807219884](https://x.com/axiommathai/status/2019449659807219884) - AxiomProver's announcement
- [x.com/CarinaLHong/status/2019454696780648640](https://x.com/CarinaLHong/status/2019454696780648640) - Context on progression from competition to research mathematics
