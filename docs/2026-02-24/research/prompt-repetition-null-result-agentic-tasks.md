# Prompt Repetition Shows Null Result on Agentic Engineering Tasks

| | |
|---|---|
| **Source** | Clouâtre.ca |
| **URL** | [clouatre.ca/posts/prompt-repetition-agent-evaluation/](https://clouatre.ca/posts/prompt-repetition-agent-evaluation/) |
| **Researched** | 2026-02-24 |

## Overview

This research tests whether prompt repetition—a technique popularized by Google's work showing benefits on retrieval benchmarks—improves agentic performance on real engineering tasks. Two controlled experiments with Claude Haiku 4.5 agents found null results: ceiling effects prevented measurable treatment differences, revealing that the technique addresses a problem that doesn't exist in well-scoped, structured coding tasks.

## Key Points

- **Experimental design**: Two parallel experiments (10 agents each, 5 control/5 treatment) evaluating prompt repetition on realistic engineering tasks: AST refactoring and security scanner implementation
- **Null finding**: Both experiments achieved ceiling effects (100% accuracy), preventing measurement of treatment effects
- **Boundary condition identified**: Prompt repetition helps benchmarks with positional retrieval (like NameIndex) but fails on tasks where agents already have structured context pointing to relevant code
- **Cost-benefit mismatch**: Doubling input tokens (~3,805 to ~7,633 characters) yields zero accuracy gain on engineering tasks
- **Methodological insight**: Baseline measurement is prerequisite to technique adoption; infrastructure constraints must be audited as potential confounders

## Technical Details

**Experiment 1**: FastMCP session ID refactor task - near-perfect baseline accuracy across all criteria

**Experiment 2**: Tree-sitter AST security scanner implementation - 100% accuracy, achieving ceiling effects

Both experiments used:
- Blind scoring against pre-registered rubrics
- Claude Haiku 4.5 model
- Verbatim prompt repetition mimicking Google Research methodology

The key insight: prompt repetition addresses positional attention decay in retrieval-based benchmarks. In engineering tasks where agents work from well-scoped source files with structured context, this attention problem doesn't manifest—the agent already has explicit pointers to relevant code.

## Implications

**For practitioners considering prompt repetition**: Measure your baseline first. Don't adopt this optimization without establishing that positional decay is actually limiting your agent's performance. On structured engineering tasks with proper context scoping, you'll double token costs without accuracy gains.

**Architectural significance**: This challenges the blanket application of general LLM techniques to agentic workflows. Agent tasks differ fundamentally from retrieval benchmarks—agents operate on bounded, structured problem spaces. Generic prompt engineering wins don't automatically transfer.

**Research methodology**: The null result itself is valuable, highlighting why pre-registration and controlled experiments matter. Infrastructure constraints (infrastructure being a confounder) must be systematically audited before attributing performance changes to technique interventions.

## Sources

- [Clouâtre.ca - Prompt Repetition Agent Evaluation](https://clouatre.ca/posts/prompt-repetition-agent-evaluation/) - Original research with controlled experiments
