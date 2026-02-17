# AetherLang - DSL for AI Workflows

| | |
|---|---|
| **Source** | Hacker News (contrario/aetherlang) |
| **URL** | [news.ycombinator.com/item?id=46954284](https://news.ycombinator.com/item?id=46954284) |
| **Researched** | 2026-02-09 |

## Overview

AetherLang is a production-ready DSL for composing AI workflows with 28 specialized node types covering LLM calls, RAG, guards, transforms, and retry logic. It provides declarative orchestration with visual debugging and async execution—solving the gap between imperative orchestration frameworks (Airflow, Prefect) and the specific primitives AI systems require. Core innovation is step-by-step node inspection tied to cost/token profiling and GPT-4o-assisted analysis.

## Key Points

- **Declarative syntax over imperative**: Named nodes with typed inputs/parameters connected by arrows eliminate boilerplate and expose flow structure visually
- **Node type specialization**: 28 node categories (Guards, LLM, RAG, Cache, Retry, Parallel, Merge) eliminate the need for custom wrappers around generic primitives
- **Built-in async execution**: Native async/await semantics with topological sort scheduling; OpenAI integration removes OAuth friction (BYOK model)
- **Visual debugger with profiling**: Step through node execution inspecting inputs/outputs; real-time cost/token tracking and latency per node
- **Multi-path convergence**: Supports guard-based branching into parallel analysis paths that merge downstream—essential for multi-perspective AI reasoning
- **No account requirement**: BYOK API keys eliminate platform lock-in and regulatory friction for enterprises

## Technical Details

**Syntax**:
```
flow ResearchPipe {
  input text query;
  node Guard: guard mode="STRICT";
  node Planner: plan steps=5;
  node RAG: rag depth=3 sources=["docs","web"];
  node LLM: llm model="gpt-4o" temp=0.2;

  Guard -> Planner -> RAG -> LLM;
  output text answer from LLM;
}
```

**Execution Model**: Workflows compile to a DAG; runtime executes via topological sort with async I/O. Node outputs become next node inputs. Profiler tracks duration, token usage, and cost per node in real-time.

**Runtime Architecture**:
- Declarative parser → AST → topological scheduler
- WebSocket-based state updates for visual debugger
- Native OpenAI client integration (no wrapper overhead)
- SVG flow rendering with multiple layout algorithms (Grid, Physics modes)

**Real-world example**: Greek handwriting OCR pipeline using sequential Cache → Recognizer → Validator nodes, reducing manual orchestration code by ~70% versus imperative SDK approach.

## Implications

**When DSL > General-Purpose Language**:
- Declarative syntax wins for straightforward DAG-like workflows (most AI pipelines)
- Visual debugging critical when debugging multi-step LLM reasoning chains
- Node specialization eliminates cognitive load managing 28 different retry/cache patterns
- Cost profiling per node justifies DSL overhead for expensive LLM calls

**When to stick with general-purpose**:
- Complex control flow (nested conditionals, loops) → Python/TypeScript still superior
- Custom node logic → SDK integration becomes friction; general-purpose code handles this cleaner
- Heterogeneous infrastructure (non-OpenAI LLMs, legacy systems) → DSL's tight OpenAI coupling is a liability

**Trade-offs**:
- **Expressiveness vs. simplicity**: Limited to DAG patterns; no arbitrary loops or backward branches
- **Debugging vs. observability**: Visual step-through is stronger than logs, but missing traces across distributed systems
- **Platform lock-in**: Reduced (BYOK keys), but DSL syntax portability is a concern if you migrate frameworks

**Practical decision matrix**: Use AetherLang for orchestrating 3-10 node chains of LLMs, RAG, and validation. Prefer orchestration platforms (Airflow, Conductor) for workflows with >15 nodes, complex scheduling, or mixed infra. Prefer code for bespoke logic and non-DAG control flow.

## Sources

- [Show HN: AetherLang – A DSL for building AI workflows with visual debugging](https://news.ycombinator.com/item?id=46954284) - Official HN discussion with author
- [GitHub - contrario/aetherlang](https://github.com/contrario/aetherlang) - Open-source repository and implementation
- [Orchestrating Asynchronous Workflows](https://orkes.io/blog/orchestrating-asynchronous-workflow/) - Context on async workflow patterns
- [LangGraph Durable Execution](https://docs.langchain.com/oss/python/langgraph/durable-execution) - Comparative async execution approach
