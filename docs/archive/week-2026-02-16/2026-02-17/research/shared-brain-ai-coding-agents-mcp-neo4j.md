# Shared Brain for AI Coding Agents: MCP Tools, Neo4j Knowledge Graph, and React Dashboard

| | |
|---|---|
| **Source** | r/ClaudeAI |
| **URL** | [old.reddit.com/r/ClaudeAI/comments/1r74e12](https://old.reddit.com/r/ClaudeAI/comments/1r74e12/i_built_a_shared_brain_for_ai_coding_agents_mcp/) |
| **Posted** | 2026-02-17 |
| **Researched** | 2026-02-17 |

## Overview

Project Orchestrator is an MCP server that addresses the fundamental statelessness problem in multi-agent AI systems: every Claude Code/Cursor/OpenAI agent session starts from zero with no memory of past decisions, architectural knowledge, or codebase context. The solution combines Neo4j graph database (for relationship-based code understanding), Meilisearch (semantic code search), and a React dashboard to create a persistent, shared knowledge layer that multiple agents can simultaneously access and update.

## Key Points

- **Core Problem Solved**: Agent sessions historically discard all context between runs, forcing re-discovery of architectural decisions and code relationships on every invocation. Project Orchestrator maintains persistent state across sessions.

- **113 MCP Tools** expose capabilities across five domains: code understanding (Tree-sitter-powered graph), semantic search, plan/task management with dependencies, knowledge capture (architectural decisions, patterns, gotchas), and multi-project workspace coordination.

- **Graph-First Architecture**: Neo4j chosen explicitly for relationship queries (impact analysis, dependency chains, call graphs). The schema is emergent, not declarative—defined implicitly by ~178 GraphStore trait methods using MERGE/CREATE/MATCH patterns rather than formal migrations.

- **Incremental Sync Pipeline**: File watcher + Tree-sitter parser keeps the knowledge graph fresh as code changes. Parses 12 languages, only re-parses changed files (via create_commit tracking) rather than full re-sync.

- **Multi-Agent Coordination**: Designed for simultaneous agent work—Agent A creates a plan, Agent B picks up tasks, Agent C records decisions, all through shared graph. No collision; structured Plans → Tasks → Steps with dependency tracking.

- **Separate Human/AI Interfaces**: Frontend dashboard (React 19 + Tailwind v4 + Jotai) is for human observation and steering, not work execution. Real work happens through MCP tool calls; dashboard is monitoring layer.

- **Stack**: Rust backend (Axum, 53K LoC), React 19 frontend (16K LoC), Tree-sitter (12 languages), Neo4j, Meilisearch, Docker Compose for single-command deployment.

## Technical Details

### Schema Management Challenges (Community-Raised)

The most critical design tension: **no formal migration system**. Schema is optional in Neo4j; nodes/relationships emerge when code writes them via Cypher patterns. This creates known drift risks:

- Renaming relationships requires updating all client.rs Cypher strings with no global consistency check
- Old data with stale labels persists silently until manually cleaned
- PART_OF vs INCLUDES_ENTITY mismatch already documented as gotcha
- MockGraphStore (HashMap-based) doesn't enforce Neo4j constraints; tests can pass against mock but fail in production

**Missing Infrastructure**:
- No schema introspection tool (no `get_neo4j_schema()` MCP tool to query actual CALL db.labels())
- No schema-as-code file (schema.cypher or equivalent)
- No startup self-check comparing expected vs actual schema
- Change-driven staleness tracking for notes (currently only time-based)

### Context Recovery Strategy

New agent sessions bootstrap context through:
1. System prompt baking in key patterns and gotchas
2. `search_notes(query)` and `get_context_notes()` for institutional knowledge
3. `search_decisions(query)` retrieving past architectural choices with rationale
4. Meta-prompting at session start to gather relevant context from stores

Not sufficient to eliminate desynchronization; depends on disciplined note maintenance.

### Multi-Language Support

Tree-sitter integration supports 12 languages for AST parsing into graph nodes: functions, structs, imports, call chains, trait implementations, enums. Relationship types include :CONTAINS, :CALLS, :IMPORTS, :IMPLEMENTS_TRAIT.

## Implications

**For Production AI Development**:
1. **Institutional Memory is Non-Trivial**: Addressing "session-zero" statelessness requires explicit architecture (graph + notes + decisions). Simple documentation isn't sufficient for agents; needs structured, queryable storage.

2. **Graph Databases Have Clear Advantages**: Relationship traversals (impact analysis, dependency graphs) become trivial with Neo4j vs relational approaches. Worth the operational overhead for agent codebases.

3. **Schema Drift is a Real Risk**: Emergent schemas work until they don't. Consider adding schema introspection tools and startup validation if adopting this pattern. The existing implementation is honest about this weakness.

4. **Multi-Agent Coordination is Solvable with Planning Layer**: Structured Plans → Tasks → Steps with dependency tracking prevents agents from clobbering each other's work. This is the practical difference between single-agent and multi-agent systems.

5. **Frontend Observation Layer Matters**: Agents need to be steerable. The React dashboard for human observation of agent decisions is essential infrastructure, not polish.

6. **Simpler Alternatives Exist for Smaller Scope**: Community consensus (BP041 comment) notes that structured JSON notes files + architectural decision logs work "good enough" for simpler production pipelines. Neo4j is optimization for complexity, not necessity.

## Community Consensus

**Positive Reception**:
- SeaMeasurement9: "I like the Neo4j use here" — clear recognition of appropriate technology choice
- HarshalN: General enthusiasm for the concept
- cristomc: Acknowledgment that this is a needed capability (though skeptical of proliferation of "AI agent brains" solutions)

**Substantive Technical Questions**:
- **Schema Updates Problem** (Firm-Space3019): Exposed as real architectural tension. The depth of the author's response acknowledging missing schema introspection tools, migration tracking, and mock drift validates the community's concern.
- **Staleness Management** (BP041): Architectural decision versioning—whether append-only or overwrite. Author's system uses time-based staleness scoring but acknowledges change-driven approach would be better.
- **Documentation Sufficiency** (Silent_Pop_8573): "Isn't this just docs?" raises the real point: agents need queryable, structured data, not prose. Docs alone aren't enough.

**Market Skepticism**: epoplive's jab ("add it to the huge pile of AI agent brains created this month") reflects genuine fatigue with proliferation of single-purpose agent frameworks. Differentiation question remains open.

## Open Questions for Practitioners

1. **Schema Governance**: If building this pattern yourself, invest upfront in schema introspection and validation tooling. The author's honest acknowledgment of missing this is a template for what to avoid.

2. **Staleness Strategy**: How will you determine when architectural decisions and notes become stale? Time-based (current) vs change-based (ideal) has material implications for multi-agent systems in active codebases.

3. **Operational Overhead**: Docker Compose deployment is simple, but managing Neo4j + Meilisearch + Rust backend adds operational surface area. Cost-benefit calculation depends on agent complexity and coordination needs.

4. **Alternative: Structured Files + API**: For smaller teams, the BP041 production approach (structured notes JSON + decision log file) might suffice. Graph DB becomes necessary only when traversals become frequent/complex.

## Sources

- [r/ClaudeAI - "I built a shared brain for AI coding agents"](https://old.reddit.com/r/ClaudeAI/comments/1r74e12/i_built_a_shared_brain_for_ai_coding_agents_mcp/) - Original post and community discussion
- [Project Orchestrator GitHub](https://github.com/this-rs/project-orchestrator) - MIT licensed, actively developed (linked in post)
