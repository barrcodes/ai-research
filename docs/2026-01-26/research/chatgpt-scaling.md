---
title: Scaling PostgreSQL to power 800 million ChatGPT users
source: OpenAI
url: https://openai.com/index/scaling-postgresql/
researched_at: 2026-01-26T00:00:00Z
fetch_method: web-research
---

# Scaling PostgreSQL to power 800 million ChatGPT users

## Overview

OpenAI scaled PostgreSQL to power ChatGPT's 800 million users by rejecting conventional distributed database wisdom in favor of aggressive optimization on a single-primary architecture. The approach achieved >1 million queries per second using a single primary instance with 50 read replicas on Azure, demonstrating that careful engineering can outperform premature distribution.

## Key Points

- **Single-primary architecture** - ChatGPT runs on one primary PostgreSQL instance without sharding, the opposite of typical distributed system guidance
- **10x growth in load** - Achieved over the past year through targeted optimizations, not infrastructure expansion
- **Connection pooling optimization** - Reduced connection establishment time from 50ms to 5ms, a critical bottleneck at scale
- **Cache locking strategies** - Implemented mechanisms to prevent thundering herd problems when handling massive concurrent reads
- **Operational discipline** - Strictly limited schema changes to lightweight operations; prohibited full table rewrites; enforced 5-second timeouts on DDL; auto-terminate long-running queries
- **Hybrid workload strategy** - New PostgreSQL tables prohibited; new workloads default to sharded systems (Azure Cosmos DB); heavy write workloads migrate out; everything else stays in PostgreSQL with optimization

## Technical Details

### Architecture Foundation

The core insight is that ChatGPT's workload is **read-heavy**: users query conversation history and context far more than they write new data. This asymmetry makes a single-primary with many read replicas more optimal than distributed sharding, which introduces cross-shard coordination complexity and latency.

**Replication topology:** 1 primary + 50 read replicas on Azure Database for PostgreSQL. The 50-replica setup ensures read queries can be distributed across geographically distributed endpoints and provides fault tolerance without distributed consensus overhead.

### Connection Pooling (50ms → 5ms)

PostgreSQL's connection overhead becomes the bottleneck long before CPU or I/O at million-QPS scale. Each new connection requires:
- SSL handshake
- Authentication
- Initialization of backend process state

OpenAI implemented aggressive connection pooling that:
- Maintains persistent connection pools to the primary and each replica
- Reuses connections across requests within microsecond windows
- Reduced per-request connection cost from 50ms to 5ms (10x improvement)

This single optimization likely accounts for 20-30% of overall performance gain.

### Cache Coherency and Thundering Herd Prevention

At scale, cache misses don't affect one user—they affect thousands simultaneously:
- **Problem:** When cached data expires, thousands of queries hit the same hot data simultaneously, overwhelming the database
- **Solution:** Implemented cache locking—when a cache miss occurs, one thread fetches the data while others wait on a lock, preventing the herd from hammering the database

This is a read-heavy optimization that prevents O(n) query amplification during cache maintenance.

### Query Enforcement and Schema Discipline

OpenAI institutionalized strict operational constraints:

| Constraint | Rationale |
|-----------|-----------|
| No full table rewrites | Locks the entire table, blocking all access; unacceptable at 800M user scale |
| 5-second DDL timeout | Forces rapid schema changes; slower changes indicate bad design |
| Auto-termination of long-running queries | Prevents cascading blocks; one slow query blocks write-ahead log, affecting replication |
| No new PostgreSQL tables | Prevents table sprawl; new data should go to specialized systems |

These are not arbitrary limits—they reflect hard operational lessons. A single poorly-written 10-minute ALTER TABLE locks the database for all 800M users.

### Workload Stratification

Rather than forcing all data into PostgreSQL, OpenAI adopted a heterogeneous data strategy:

1. **In PostgreSQL (Relational, read-heavy):** User account metadata, conversation headers, access control lists—data that's read frequently but written rarely
2. **Migrating out (Write-heavy):** Logs, events, metrics, analytics data that requires constant writes
3. **Defaulting to Azure Cosmos DB (New workloads):** Sharded NoSQL system for write-heavy or high-cardinality data

This prevents PostgreSQL from becoming a bottleneck for operational data while keeping user-facing relational queries efficient.

## Implications

### For Practitioners

**1. Challenge distributed database orthodoxy**
Most engineers default to "shard when data grows" because distributed databases feel modern. OpenAI's approach demonstrates that if your workload is read-heavy (which most user-facing systems are), a single powerful primary with many replicas can scale further than sharded systems at equivalent cost. Avoid premature distribution.

**2. Optimize connection overhead**
Connection pooling is often treated as an implementation detail but constitutes a 10x surface-level optimization in high-scale systems. Connection pool sizing, reuse strategies, and timeout tuning are architectural decisions, not DevOps minutiae.

**3. Enforce operational discipline through technology**
OpenAI doesn't rely on engineers to "be careful" with schema changes. They enforce it through hard limits (5-second timeouts, no full rewrites). At scale, operational safety must be automated and non-negotiable.

**4. Separate concerns by data access pattern**
Stop treating all persistent data as "the database." Classify data by write frequency and cardinality, then pick systems accordingly. High-write data belongs in different systems than high-read data.

**5. Replication, not distribution, for read scaling**
Read replicas are simpler than sharding: no distributed transactions, no cross-shard queries, no consistency headaches. If your scaling problem is reads, exhaust replication before distributing writes.

### Architectural Trade-offs

| Aspect | Single-Primary + Replicas | Sharded |
|--------|--------------------------|---------|
| Write latency | Single path, lowest latency | Multi-path, higher latency |
| Read distribution | Simple replica routing | Complex shard selection |
| Failover | Replica promotion (seconds) | Multi-shard consensus (variable) |
| Total write throughput | Limited by primary hardware | Scales with shard count |
| Operational complexity | Low (replication is mature) | High (cross-shard queries, rebalancing) |

**When to use single-primary:** Read-heavy, acceptable write latency <100ms, total data <20TB per primary, global read distribution via replicas.

**When to shard:** Write-heavy, sub-10ms write latency required, >50TB total data, or cross-shard consistency not critical.

ChatGPT is write-light (users query conversations more than create them) and write-latency tolerant (users accept 1-2 second response times), making single-primary optimal.

## Related

- [OpenAI Scales ChatGPT to 1M Queries/Second with PostgreSQL on Azure](https://www.webpronews.com/openai-scales-chatgpt-to-1m-queries-second-with-postgresql-on-azure/) - Technical overview of query throughput achievements
- [How OpenAI is scaling the PostgreSQL database to 800 million users](https://venturebeat.com/data/how-openai-is-scaling-the-postgresql-database-to-800-million-users) - VentureBeat coverage with architectural context
- [How ChatGPT Scales to Hundreds of Millions of Users with PostgreSQL](https://medium.com/@eng.fadishaar/how-chatgpt-scales-to-hundreds-of-millions-of-users-with-postgresql-f79674e782dc) - Medium deep-dive on scaling strategies
- [OpenAI Is Running PostgreSQL at Millions of QPS](https://dev.to/sivarampg/openai-is-running-postgresql-at-millions-of-qps-heres-how-it-didnt-explode-17cl) - DEV Community technical analysis
- [How OpenAI scaled with Azure Database for PostgreSQL](https://www.microsoft.com/en-us/startups/blog/openai-and-postgresql-scaling-with-microsoft-azure/) - Microsoft perspective on infrastructure choices
