# Writing Code Is Cheap Now

| | |
|---|---|
| **Source** | Simon Willison's Guide to Agentic Engineering Patterns |
| **URL** | [simonwillison.net/guides/agentic-engineering-patterns/code-is-cheap/](https://simonwillison.net/guides/agentic-engineering-patterns/code-is-cheap/) |
| **Researched** | 2026-02-23 |

## Overview

AI agents have fundamentally inverted the economics of code generation, making the act of writing code cheap—but not quality code. This shift demands engineers reconsider decades of cost-benefit intuitions around refactoring, testing, documentation, and experimentation. The challenge now centers on maintaining quality gates and developing organizational practices suited to abundance rather than scarcity.

## Key Points

- **Economics Inversion**: Code generation cost has collapsed due to agentic systems, invalidating traditional time-investment calculations that shaped modern engineering practices
- **Quality vs. Generation Gap**: Producing code differs fundamentally from producing *good* code—functional, maintainable, tested, documented, and architecturally sound solutions remain the real constraint
- **Parallel Execution**: Multiple agents working simultaneously across codebases enable concurrent implementation, refactoring, testing, and documentation—amplifying throughput beyond single-engineer capacity
- **Reconsidering Skepticism**: Engineers should default to "why not?" for speculative features and experiments in asynchronous contexts where failed attempts carry minimal organizational cost
- **Organizational Hiatus**: Industry lacks established best practices for agentic workflows—teams must develop new habits around quality gates, agent orchestration, and validation

## Technical Details

The article frames "good code" across seven dimensions:

1. Functionally correct and validated against requirements
2. Solves the right problem (not just what was asked)
3. Error handling and edge cases covered
4. Simple, readable, maintainable design
5. Protected by appropriate test coverage and documentation
6. Amenable to future modifications
7. Meets non-functional standards (security, accessibility, scalability)

The shift enables parallel development flows where different agents handle different concerns simultaneously—but orchestration, validation, and integration remain manual or require sophisticated testing frameworks.

## Implications

**For Architecture**: Design systems expecting frequent refactoring and iteration. Code volatility increases; invest in test suites and architectural boundaries that absorb change.

**For Teams**: Reframe code review as quality gates (does this meet all seven criteria?) rather than cost-justification ("is this worth writing?"). Accept that agents will generate more surface area—focus human effort on validation, integration, and architectural coherence.

**For Career Development**: Shift from "velocity as lines of code" to "quality decision-making at architectural scale." The value migrates toward problem decomposition, testing strategies, and knowing what not to build.

**Practical Next Step**: Run asynchronous agent experiments with minimal organizational risk. Use outcomes to establish new baseline intuitions for what's "worth building"—the answer changes when the cost is nearly zero.

## Sources

- [Simon Willison's Agentic Engineering Patterns Guide](https://simonwillison.net/guides/agentic-engineering-patterns/code-is-cheap/) - Comprehensive guide on shifting economics and practices for agentic development
