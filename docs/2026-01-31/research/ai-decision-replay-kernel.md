# Show HN: OSS kernel for replaying and diffing AI decisions

| | |
|---|---|
| **Source** | Hacker News |
| **URL** | [news.ycombinator.com/item?id=46841302](https://news.ycombinator.com/item?id=46841302) |
| **Researched** | 2026-01-31 |

## Overview

Verist is a deterministic workflow kernel addressing production AI system challenges: explainability, reproducibility, and safe model iteration. Rather than in-memory state management, it uses a database-backed audit log as the source of truth, enabling exact replay of previous decisions and precise diffing across model/prompt changes.

## Key Points

- **Database-first architecture**: All AI decisions stored as artifacts with full audit trail; enables exact replay without re-running LLM calls
- **Typed step definitions**: Explicit Zod schemas for inputs/outputs eliminate hidden state; workflows are transparent and composable
- **Diff-as-first-class**: Compare outputs across model versions or prompt changes deterministically; identify behavioral regressions before shipping
- **Minimalist design philosophy**: Avoids framework bloat; focuses exclusively on auditability and reproducibility rather than convenience
- **Git-like workflows**: Version control paradigm applied to AI decisions; step-by-step transparency for debugging and compliance

## Technical Details

Core packages include `@verist/core` (kernel: `defineStep`, `defineWorkflow`, `run`), `@verist/replay` (artifact capture and recompute), `@verist/storage` with PostgreSQL adapter, and `@verist/llm` (structured tracing).

Step definition pattern:
```typescript
defineStep({
  name: string,
  input: ZodSchema,
  delta: ZodSchema,
  run: async (input, ctx) => ({ delta, events })
})
```

Execution preserves complete decision history in the database; replay reuses stored artifacts; diffs recompute with alternative models/prompts while keeping inputs identical.

## Implications

**When to use**: Production systems requiring auditability, compliance logging, or frequent model experimentation. Teams needing to justify AI decisions to stakeholders or regulators.

**Trade-off**: Verist trades execution speed and framework convenience for determinism and transparency. Not designed for latency-critical streaming agents; optimized for batch workflows and async operations.

**Architectural fit**: Complements agentic systems by replacing in-memory execution with persistent, queryable decision history. Enables separation of concerns: agents define logic, Verist captures and replays it.

**Practical use**: Capture production traces, then replay with experimental prompts locally to validate changes before deployment. Build diffs to measure prompt engineering impact.

## Sources

- [Verist Repository](https://github.com/verist-ai/verist) - Open-source TypeScript implementation
- [Hacker News Discussion](https://news.ycombinator.com/item?id=46841302) - Community feedback and use cases
