# Broodlink - Multi-Agent AI Orchestration in Rust

| | |
|---|---|
| **Source** | GitHub Repository |
| **URL** | [github.com/nevenkordic/broodlink](https://github.com/nevenkordic/broodlink) |
| **Researched** | 2026-02-19 |

## Overview

Broodlink is a production-oriented multi-agent orchestration platform enabling AI agents to collaborate through shared infrastructure. Rather than isolated agent instances, Broodlink coordinates agent work across semantic memory, intelligent task routing, and workflow execution—implementing both MCP (Model Context Protocol) and Google's Agent-to-Agent patterns.

## Key Points

- **Distributed Coordination via NATS**: Task routing uses weighted scoring (success rate, load cost, recency) for intelligent agent assignment rather than simple queue mechanics
- **Semantic Memory Layer**: Two-tier architecture combining Dolt (versioned writes) with outbound polling to Qdrant embeddings (768-dim nomic-embed-text), enabling memory-aware agent decisions
- **Polyglot Infrastructure**: Central tool API (beads-bridge) exposes 61 standardized tools via HTTP/JWT RS256 to heterogeneous agents; vector search decoupled from primary datastore
- **Heartbeat-Driven Consistency**: 5-minute synchronization cycles handle database commits and agent deactivation detection, reducing operational complexity vs. real-time coordination

## Technical Details

**Data Flow**:
- Agents write semantic memory → Dolt (transactional) + outbox row
- Embedding worker polls outbox every 2s → vectorizes + inserts to Qdrant
- Coordinator scores eligible agents across four dimensions before task claiming (atomic)

**Storage Stack**:
- Dolt (versioned storage, port 3307)
- PostgreSQL (high-throughput ops, port 5432)
- Qdrant (vector index, 768-dim, port 6333)
- NATS (message backbone, port 4222)

**Test Coverage**: 129 unit tests, 15 integration suites, 69 end-to-end tests indicate maturity focus.

## Implications

**For Practitioners**:
- **When to Use**: Multi-agent deployments where task distribution, memory sharing, and cost/load optimization matter; teams managing heterogeneous agent types (different model providers, capabilities)
- **Architecture Fit**: Assumes async task workflows; heartbeat consistency model suits 5+ minute horizon problems, not sub-second coordination
- **Trade-offs**: Outbound embedding polling (2s latency) simplifies worker scalability but delays vector freshness; Dolt versioning adds operational complexity vs. immutable logs

**Integration Points**: MCP and A2A protocol support enables integration with broader agent ecosystems, though tool standardization (61 HTTP-exposed functions) requires upfront API design.

**Rust as Foundation**: Type safety, async/await patterns, and single-language build reduce deployment complexity vs. polyglot services—but limits language heterogeneity in agent implementations.

## Sources

- [nevenkordic/broodlink](https://github.com/nevenkordic/broodlink) - Open-source multi-agent orchestration platform with NATS coordination, semantic memory, and MCP support
