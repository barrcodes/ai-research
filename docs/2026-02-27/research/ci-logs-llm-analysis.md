# We Gave Terabytes of CI Logs to an LLM

| | |
|---|---|
| **Source** | Mendral |
| **URL** | [mendral.com/blog/llms-are-good-at-sql](https://www.mendral.com/blog/llms-are-good-at-sql) |
| **Researched** | 2026-02-27 |

## Overview

Mendral built a practical system that exposes CI logs (1.5B lines/week) via a SQL interface to an LLM agent, eliminating predefined tool limitations. The agent writes its own queries against ClickHouse, enabling forensics on novel failure modes without requiring engineers to anticipate investigation patterns. This surfaces a critical architectural insight: **without fresh, queryable data, agents cannot answer "did I break this, or was it already broken?"**

## Key Points

- **Direct SQL, not tool wrappers**: Agent constructs queries instead of choosing from predefined tools. This flexibility means investigators can follow unexpected failure patterns without API redesign.
- **Aggressive denormalization**: All 48 columns of metadata (commit SHA, author, branch, workflow name) embedded in every log row. ClickHouse's columnar compression handles it (35:1 overall, 301:1 on repetitive fields).
- **Dual query modes**: Job metadata (63% of queries, ~47K rows median) hits materialized views; raw logs (37%, ~1.1M rows) scan full text. Typical investigation: 4.4 queries scanning 335K rows.
- **GitHub API rate-limit solved via durable suspensions**: Capped ingestion at 3 req/sec, reserved 4K req/hour for agent. Inngest durable execution suspends (not fails) on rate limit, resuming exactly where stopped.
- **Linear scaling, not exponential**: Query latency scales 1:1 with row count. P95 queries scan 940M rows; heaviest investigations hit 4.3B rows.

## Technical Details

**Storage & Indexing**:
- 5.31 TiB raw → 154 GiB on disk (21 bytes per log line including metadata)
- Primary key: (org, timestamp, repository, run_id)
- Bloom filters on 14 columns; ngram indexes on log content
- 60% of queries return <50ms

**Ingestion Pipeline**:
- 1.5 billion lines, 700K jobs/week
- GitHub API bottleneck: 15K requests/hour limit vs. unbounded CI pipeline
- Solution: Rate-limit ingestion, queue overages via Inngest, target <5min P95 ingestion delay

**Agent Query Analysis** (8,534 sessions, 52,312 queries):
- Median: 335K rows scanned across 3 queries
- P95: 940M rows
- Heavy: 4.3B rows for complex forensics

## Implications

**When to adopt this pattern**: Your org needs to investigate CI failures without waiting for engineers to design new query endpoints. If failure modes are diverse (flakes, infra issues, integration bugs), predefined tools become a bottleneck. SQL-native approach scales to unexpected investigation paths.

**Trade-offs**:
- Requires stable, low-latency SQL storage (ClickHouse or similar). Traditional log aggregators (ELK, Splunk) lack the structured access patterns needed.
- Aggressive denormalization increases write volume but enables query flexibility. Normalization would require multi-table joins that LLMs struggle to compose optimally.
- Rate-limiting complexity (Inngest durable execution) is non-trivial, but prevents cascading failures when CI velocity exceeds API budgets.

**Alternatives**:
- Pre-computed dashboards + fixed tool API (loses flexibility, misses novel failure modes)
- Streaming log processors (Kafka/Flink) without queryable storage (requires redesign for each investigation)
- LLM-as-cache with ephemeral context (loses ability to trace across runs)

**For practitioners**: If you're building agent-driven diagnostics, prioritize queryable, structured storage over flexible indexing. The LLM's ability to compose novel queries is only valuable if data ingestion is fast, fresh, and durable. Make rate-limiting transparent to the agent via task suspension, not failures.

## Sources

- [LLMs Are Good at SQL. We Gave Ours Terabytes of CI Logs.](https://www.mendral.com/blog/llms-are-good-at-sql) - Mendral's technical deep-dive on their CI log analysis architecture
- [We gave terabytes of CI logs to an LLM](https://news.ycombinator.com/item?id=47181801) - Hacker News discussion with additional context
- [Anatomy of a Production AI Agent](https://www.mendral.com/blog/anatomy-of-a-production-ai-agent) - Related Mendral article on production agent patterns
