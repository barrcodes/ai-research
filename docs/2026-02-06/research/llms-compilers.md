# LLMs could be, but shouldn't be compilers

| | |
|---|---|
| **Source** | Alperen Keles |
| **URL** | [alperenkeles.com/posts/llms-could-be-but-shouldnt-be-compilers](https://alperenkeles.com/posts/llms-could-be-but-shouldnt-be-compilers/) |
| **Researched** | 2026-02-06 |

## Overview

This article critiques the architectural pattern of treating LLMs as compilers—code generators that abstract away implementation details. The core argument isn't about hallucinations, but about the fundamental semantic underspecification of natural language. Unlike formal compilers with defined guarantees, LLMs make arbitrary choices about data models, edge cases, and tradeoffs without formal validation.

## Key Points

- **Underspecification Problem**: Natural language permits multiple valid interpretations. LLMs must arbitrarily resolve edge cases, error handling, and performance tradeoffs that a proper specification would formalize.

- **Loss of Guarantees**: Traditional compilers reduce cognitive load while maintaining *defined semantics and testable guarantees*. LLM-generated code loses control without bounding the range of possible unintended behaviors through formal language constraints.

- **Specification Avoidance**: LLMs create perverse incentives to skip precise specification work. Developers become "consumers of software you meant to produce"—iteratively guessing rather than deliberately constructing systems.

- **No Formal Validation**: Unlike garbage collection or memory management (bounded abstractions), LLM outputs can't be formally validated against actual requirements. Only post-generation inspection reveals misalignments.

## Technical Details

The distinction is crucial: compilers operate within well-specified semantic domains (syntactic rules, type systems, memory models). LLMs operate in the space of natural language ambiguity. A compiler can guarantee certain properties hold—type safety, termination, determinism. LLM-generated code provides no such guarantees because the input specification itself lacks formal rigor.

The author contrasts this with traditional abstraction layers. Garbage collection abstracts memory while maintaining invariants. LLM code generation abstracts specification while abandoning formal guarantees—fundamentally different risk profiles.

## Implications

**For architects**: Resist treating LLMs as a substitute for specification. LLM-assisted code generation works tactically (boilerplate, patterns), but fails strategically as a compiler replacement. The cognitive burden you think you're eliminating (specification writing) will resurface as code review, debugging, and iterative refinement.

**Decision point**: Use LLMs to accelerate well-specified designs, not to replace specification work. The pattern works when human architects define semantics precisely; it breaks when developers hope LLMs will infer their actual requirements.

**Practical alternative**: Employ LLMs for constrained generation (templates, parsers, tests) within formal language frameworks, not for autonomous system design decisions.

## Sources

- [LLMs could be, but shouldn't be compilers](https://alperenkeles.com/posts/llms-could-be-but-shouldnt-be-compilers/) - Alperen Keles' argument against LLM-as-compiler architectural patterns
