# Scaling PostgreSQL to power 800 million ChatGPT users

| | |
|---|---|
| **Source** | OpenAI |
| **URL** | [openai.com/index/scaling-postgresql/](https://openai.com/index/scaling-postgresql/) |
| **Researched** | 2026-01-31 |

## Overview

OpenAI has scaled a single-primary PostgreSQL instance to handle millions of queries per second for 800 million ChatGPT users, paired with nearly 50 read replicas distributed globally. Despite a 10x load increase over the past year, this architecture delivers low double-digit millisecond p99 latency and five-nines availability through rigorous optimizations and load isolation strategies that transform PostgreSQL's traditional weaknesses into a viable scale-out solution.

## Key Points

- **Single-primary architecture at scale**: OpenAI operates a single-primary PostgreSQL instance with approximately 50 read replicas across regions, handling millions of QPS for read-heavy workloads. Writes remain concentrated on the primary to avoid the complexity of sharding.

- **MVCC write amplification as a design constraint**: PostgreSQL's multiversion concurrency control (MVCC) implementation creates significant write overhead—each row update copies the entire row and generates dead tuples, increasing both write and read amplification. This fundamental limitation dictates architectural decisions: write-heavy workloads are migrated to sharded systems (Azure CosmosDB) while keeping PostgreSQL for read-dominant workloads.

- **Multi-layer rate limiting as chaos mitigation**: Rate limiting across application, connection pooler, proxy, and query layers prevents cascade failures. Traffic spikes from cache misses, expensive joins, or retry storms are contained through targeted load shedding and query digest blocking.

- **Workload isolation as failure boundary**: High-priority and low-priority requests are isolated to separate PostgreSQL instances. Product-level isolation ensures that one service's resource-intensive spike doesn't degrade others—addressing the "noisy neighbor" problem that has historically caused production incidents.

- **Connection pooling with PgBouncer reduces latency by 10x**: Deploying PgBouncer in statement/transaction mode reduced connection setup from 50ms to 5ms by reusing connections. Co-locating proxy, clients, and replicas in regions minimizes network overhead.

- **Cache locking prevents miss storms**: When cache hits drop unexpectedly, a single-writer cache lock mechanism prevents multiple requests from all hitting PostgreSQL for the same missing key. Only one acquirer fetches from the database while others wait, preventing cascading read spikes.

- **Cascading replication (in testing) unlocks horizontal scale**: To overcome WAL shipping bottlenecks as replica count grows, intermediate replicas will relay WAL to downstream replicas, enabling scale to potentially 100+ replicas without overloading the primary. This introduces operational complexity around failover management.

- **High-Availability with hot standby mitigates single points of failure**: The primary runs with a continuously synchronized replica ready for immediate promotion. Offloading read traffic to replicas means primary failures no longer become SEV-0—reads continue while writes pause.

- **Conservative schema management**: Schema changes limited to lightweight operations (adding/dropping columns that don't trigger table rewrites), with strict 5-second timeouts. Field backfills use aggressive rate limiting and can take weeks. No new tables on PostgreSQL; they go to CosmosDB.

- **Query optimization discipline**: Complex multi-table joins (12-table joins identified as a past incident trigger) are avoided; complex logic moves to the application layer. ORM-generated queries are carefully reviewed as they often violate OLTP patterns. Idle transaction timeouts prevent blocking autovacuum.

## Technical Details

**Architecture at scale:**
- Single primary on Azure PostgreSQL flexible server, ~50 read replicas across regions
- PgBouncer proxy layer in statement/transaction mode co-located with replicas
- Replica lag maintained near zero despite millions of QPS
- Connection limit addressed through pooling (5,000 max connections per instance)

**Scaling mechanisms:**
- Read-heavy workloads remain on PostgreSQL; write-heavy workloads migrated to Azure CosmosDB
- Caching layer absorbs most read traffic; cache locking prevents miss storms
- Rate limiting at four layers (app, pooler, proxy, query) prevents resource exhaustion
- Workload isolation via separate instances for high/low-priority tiers

**Performance characteristics:**
- Low double-digit millisecond p99 client-side latency
- 99.99999% availability (five-nines)
- Only one SEV-0 incident in 12 months (during ImageGen viral launch with 10x+ write spike)
- 10x load growth over past year sustained without architecture changes

**Future work:**
- Cascading replication testing for safe multi-tier WAL relay
- Continued migration of remaining write-heavy workloads to CosmosDB
- Exploration of sharded PostgreSQL or alternative distributed systems as fallback

## Implications

For architects operating database-intensive services at scale:

1. **Single-primary PostgreSQL is viable to continental scale if workload separation is disciplined.** The traditional advice to shard early is expensive and complex; maintaining single-primary while partitioning workloads (read vs. write, high vs. low priority) defers sharding until it's truly necessary.

2. **Connection pooling and co-location are first-order optimizations.** 10x latency reduction through PgBouncer and region-aware topology demonstrates that network I/O and connection handshake overhead are often underestimated in distributed systems design.

3. **MVCC is a fundamental write-scaling ceiling, not a bug to work around.** Rather than trying to optimize PostgreSQL for mixed workloads, accept the constraint and architect around it: read-heavy on PostgreSQL, write-heavy on alternative systems. This simplifies operations vs. trying to tune autovacuum endlessly.

4. **Cache-miss storms are a primary failure mode requiring architectural defense.** Cache locking (single writer per key) is a pattern worth implementing universally. This is more important than cache eviction policies.

5. **Rate limiting at multiple layers prevents cascade failures better than capacity headroom.** Explicit load shedding at application, pooler, proxy, and query levels is more reliable than hoping CPU/memory headroom absorbs surprises.

6. **Schema change risk increases quadratically with table size.** At OpenAI's scale, even "lightweight" schema changes require 5-second timeouts and field backfills can take weeks. Plan immutable schema contracts early.

7. **Workload isolation (separate instances by priority/product) is a low-cost insurance policy.** Prevents blast radius of noisy neighbors and provides operational flexibility to mitigate resource spikes without global load shedding.

## Sources

- [OpenAI: Scaling PostgreSQL to power 800 million ChatGPT users](https://openai.com/index/scaling-postgresql/) - Full article with architecture diagrams and optimization strategies, published January 22, 2026
