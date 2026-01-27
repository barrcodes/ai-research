---
title: Scaling PostgreSQL to power 800 million ChatGPT users
source: OpenAI
url: https://openai.com/index/scaling-postgresql/
researched_at: 2026-01-27T00:00:00Z
fetch_method: playwright
---

# Scaling PostgreSQL to power 800 million ChatGPT users

## Overview

OpenAI scaled a single-primary PostgreSQL instance to handle millions of queries per second across 800 million users through systematic optimization rather than architectural redesign. The unsharded architecture—with a single writer and ~50 read replicas—continues to provide sufficient runway despite 10x load growth in the past year, achieving five-nines availability with only one SEV-0 incident over 12 months.

## Key Points

- **Single-primary architecture sustained at massive scale**: Contrary to conventional wisdom, a single writer remains viable for read-heavy workloads (ChatGPT, API platform) when properly optimized, avoiding the complexity of sharding existing application endpoints

- **Write-heavy workloads migrated offboard**: Shardable write-heavy workloads pushed to Azure Cosmos DB; remaining writes minimized through application-level optimization (lazy writes, strict backfill rate limits, redundancy elimination)

- **Replica scaling approaching limits**: ~50 read replicas require cascading replication in development to scale beyond current infrastructure without overwhelming primary's WAL shipping throughput

- **MVCC write amplification as architectural constraint**: PostgreSQL's tuple versioning creates significant overhead under write spikes—entire rows copied for single-field updates, dead tuples must be scanned, autovacuum tuning complexity

- **Multi-layer load shedding critical**: Rate limiting applied at application, connection pooler, proxy, and query levels prevents retry storms and cascade failures during unexpected traffic spikes

## Technical Details

### Architecture Decisions

**Single-Primary Rationale**: Sharding would require changes to hundreds of application endpoints over months/years. Current read-heavy workload distribution (primary handles writes + essential transaction-scoped reads; replicas handle everything else) provides 10x+ runway. Future sharding not ruled out but lower priority than pushing PostgreSQL further.

**Replication Strategy**:
- 1 primary (Azure PostgreSQL flexible server)
- 1 hot standby in HA mode for failover
- ~50 read replicas across geographic regions
- Cascading replication in development to enable 100+ replicas without overwhelming primary

### Optimization Layers

1. **Primary Load Reduction**
   - Offload read queries to replicas wherever possible
   - Migrate shardable, write-heavy workloads to CosmosDB
   - Aggressive application optimization: eliminate redundant writes, introduce lazy writes, enforce strict rate limits on backfill operations

2. **Query Optimization**
   - Eliminate multi-table joins (identified 12-table join causing past SEVs)
   - Move complex join logic to application layer
   - Audit ORM-generated SQL for anti-patterns
   - Configure idle_in_transaction_session_timeout to prevent blocking autovacuum

3. **Connection Pooling**
   - PgBouncer proxy layer in statement/transaction mode
   - Reduced connection setup latency 50ms → 5ms
   - Co-locate proxy, clients, replicas per region to minimize inter-region overhead
   - Careful idle timeout configuration critical to prevent exhaustion

4. **Cache Locking/Leasing Mechanism**
   - Single winner pattern: only one request acquires lock on cache miss for a key
   - Other requests wait for cache repopulation rather than thundering herd to PostgreSQL
   - Prevents cascade failures during cache-miss storms

5. **Workload Isolation**
   - Split traffic into low-priority and high-priority tiers on separate instances
   - Product-level isolation prevents one service degradation from affecting others
   - Mitigates noisy-neighbor problem during feature launches with inefficient queries

6. **Schema Management**
   - Only lightweight operations permitted (column add/drop, concurrent index creation)
   - 5-second timeout on schema changes
   - Backfill operations rate-limited, can take 1+ week to maintain stability
   - All new table requirements directed to CosmosDB instead of PostgreSQL

### Performance Metrics

- **Latency**: Low double-digit millisecond p99 client-side latency
- **Availability**: Five-nines (99.999%) in production
- **Incidents**: Only 1 SEV-0 over 12 months (viral ChatGPT ImageGen launch when write traffic spiked 10x with 100M+ new signups)
- **Throughput**: Millions of QPS, near-zero replication lag across replicas

## Implications

### For Infrastructure Teams

**Single-primary PostgreSQL remains viable longer than expected**: Organizations should evaluate workload characteristics before pursuing immediate sharding. Read-heavy systems (like LLM inference) can sustain enormous scale with disciplined optimization—this delays the complexity tax of distributed transactions and sharding logic.

**MVCC becomes limiting factor before connection/network limits**: The write amplification and tuple scanning overhead of PostgreSQL's MVCC surfaces before infrastructure capacity is exhausted. Plan write-heavy workload migration early; don't wait for primary overload.

**Multi-layer rate limiting prevents cascade failures**: Single-layer rate limiting insufficient. Implementing across application, pooler, proxy, and query layers provides defense-in-depth against retry storms and unexpected traffic spikes from feature launches or cache failures.

### For Practitioners Building on Postgres at Scale

1. **Separate read paths from write paths aggressively**: Make replica reads the default; writes only on primary. This disproportionately improves resilience (reads survive primary failure).

2. **Audit ORM-generated SQL continuously**: Complex joins and missing indexes often come from ORMs. Need systematic SQL review—don't assume frameworks generate optimal queries at your scale.

3. **Budget for long-duration backfill operations**: Schema changes are slow with rate limiting. Plan feature work with weeks-long backfill windows, not hours.

4. **Co-locate resources per region**: PgBouncer latency reduction (50ms → 5ms) from co-location demonstrates the cost of cross-region connections. Replicate the pattern across your infrastructure.

5. **Prepare for cascading replication operationally**: Scaling beyond ~50 replicas requires cascading replication but adds failover complexity. Test failure modes (intermediate replica crashes, cascade lag) before production.

## Related

- [The Part of PostgreSQL We Hate the Most](https://www.cs.cmu.edu/~pavlo/blog/2023/04/the-part-of-postgresql-we-hate-the-most.html) - Bohan Zhang & Andy Pavlo deep-dive on MVCC limitations
- [Azure PostgreSQL Cascading Replication Documentation](https://www.postgresql.org/docs/current/warm-standby.html#CASCADING-REPLICATION) - Feature in testing for OpenAI's next scaling phase
- [PostgreSQL MVCC Architecture](https://www.postgresql.org/docs/current/mvcc.html) - Reference on tuple versioning mechanisms
