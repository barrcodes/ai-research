---
title: Niwa - Async Conflict-Aware Spec/Planning for LLM Agents
source: GitHub - secemp9/niwa
url: https://github.com/secemp9/niwa
researched_at: 2026-01-28T00:00:00Z
---

# Niwa: Async Conflict-Aware Spec/Planning for LLM Agents

## Overview

Niwa is a document coordination system designed for multi-agent LLM workflows that need collaborative editing with strict conflict detection and resolution. Rather than last-write-wins semantics, it treats document edits as versioned operations with explicit conflict tracking per agent per section, enabling safe parallel work on specifications and planning documents.

## Key Points

- **Node-level conflict detection**: Documents parse into hierarchical markdown sections (headings) with independent version numbers; conflicts surface only when agents edit the same node without accounting for intervening changes
- **Agent-scoped state**: Each agent maintains separate tracking of pending reads and unresolved conflicts, surviving context switches and token-limit-enforced session resets
- **Four resolution modes**: ACCEPT_YOURS, ACCEPT_THEIRS, ACCEPT_AUTO_MERGE, or MANUAL_MERGE—defaulting to manual for safety-critical specs
- **LMDB-backed persistence**: Memory-mapped storage provides ACID transactions and concurrent access without locks; includes full audit trail and rollback capability
- **Claude Code hooks**: Automatic context injection at session start and before compaction ensures agents remember command syntax across conversation continuations

## Technical Details

Niwa uses markdown-it-py for correct GFM parsing (tables, task lists, frontmatter), avoiding regex fragility around `#` in code blocks. Documents serialize into three LMDB tables: nodes (content + version), pending reads (per agent), and conflicts (both versions stored).

Node IDs are static (`h{level}_{index}`) making them stable references across sessions. Conflict resolution is conservative: mismatched version numbers on write trigger conflict storage rather than merge. Auto-merge applies basic structural combination; semantic understanding requires manual intervention or LLM judgment.

No explicit performance benchmarks published, but design emphasizes non-blocking concurrent reads/writes and lazy evaluation for large documents.

## Implications

**When to use**: Specifications, implementation plans, or design docs edited collaboratively by multiple agents where overwrites create silent inconsistencies. Particularly valuable when agents operate under token limits requiring session breaks—conflicts survive these transitions.

**Trade-off**: Manual merge requirement prioritizes auditability and safety over convenience. For chat-based document editing where agents can negotiate in-band, this is appropriate. For automated pipelines expecting silent merging, this creates friction requiring explicit conflict handling.

**Architectural fit**: Works well in agentic patterns where a planning agent sketches specs and execution agents refine sections. The conflict journal provides audit trail for blame/traceability. Not suitable for high-frequency operational logs where conflict rate would be prohibitive.

**Integration concern**: Requires `.claude/settings.json` for Claude Code hooks; setup friction for new deployments. LMDB single-database-per-directory design limits sharding.

## Related

- [Multi-agent coordination patterns](https://example.com) - Complementary approaches to agent synchronization
- [LMDB performance characteristics](http://www.lmdb.tech/bench/) - Underlying storage engine
- [Markdown AST parsing considerations](https://example.com) - Why regex-based markdown parsing fails
