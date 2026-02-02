# Procedural Long-Term Memory: 99% Accuracy on 200-Test Conflict Resolution Benchmark

| | |
|---|---|
| **Source** | Reddit r/MachineLearning |
| **URL** | [old.reddit.com/r/MachineLearning/comments/1qr4wpg/r_procedural_longterm_memory_99_accuracy_on/](https://old.reddit.com/r/MachineLearning/comments/1qr4wpg/r_procedural_longterm_memory_99_accuracy_on/) |
| **Researched** | 2026-01-30 |

## Overview

A student researcher presented a novel memory architecture for LLMs achieving 99% accuracy (198/200) on a comprehensive conflict resolution benchmark, representing a +32.1 percentage point improvement over state-of-the-art (Mem0 at 66.9%). The system introduces explicit semantic conflict detection through dual-graph modeling, multi-judge consensus mechanisms, and biologically-inspired memory decay patterns with reconsolidation.

## Key Points

- **Performance**: 99% accuracy with 3.7ms per test (270 tests/second), production-validated at 1000 concurrent users with <200ms p95 latency
- **Multi-Judge Architecture**: Four specialized judges (Safety, Memory, Time, Consensus) with grammar-constrained JSON output via Outlines library, eliminating hallucination in validation layers
- **Dual-Graph Modeling**: Separates substantiated facts (S ≥ 0.9) from uncertain inferences, enabling explicit uncertainty quantification and epistemic tracking
- **Biologically-Grounded Memory Dynamics**: Type-specific Ebbinghaus decay (INVARIANT: 0.0, ENTITY: 0.01/day, PREFERENCE: 0.08/day, STATE: 0.5/day) with reconsolidation on retrieval
- **Hybrid Conflict Detection**: Three-stage pipeline combining rule-based validation (deterministic, fast), embedding similarity (pgvector), and ontology constraints

## Technical Details

### Architecture Stack

**Storage Layer**: Neo4j (graph database), PostgreSQL + pgvector (semantic embeddings), Redis (caching)

**Compute Layer**: FastAPI, Celery (async workers), sentence-transformers for embeddings

**Constraint System**: Outlines library for grammar-constrained generation, eliminating probabilistic hallucination in validation decisions

**Infrastructure**: Kubernetes auto-scaling, Prometheus + Grafana monitoring

### Benchmark Composition

- Basic conflicts (21 tests): 100%
- Complex scenarios (20 tests): 100%
- Advanced reasoning (19 tests): 100%
- Edge cases (40 tests): 100%
- Real-world scenarios (60 tests): 98%
- Stress tests (40 tests): 98%

The 2% failure rate concentrated in real-world and stress scenarios suggests the system handles synthetic/controlled conditions perfectly but encounters edge cases in noisy production data.

### Memory Mechanics

The reconsolidation mechanism mirrors biological memory where retrieval strengthens rather than retrieves static facts. Type-specific decay models semantic stability: invariants never decay (e.g., identities), entities decay slowly (core identity properties), preferences/opinions decay faster, and volatile states (current location, mood) decay rapidly. This creates a spectrum from long-term semantic knowledge to short-term contextual state.

## Implications

**For LLM-Based Agents**: This addresses a critical gap in current agentic systems—conflict resolution and memory consistency. Most agents lack explicit mechanisms for detecting contradictions across stored context. The consensus judge pattern could inform multi-model architectures where agreement/disagreement signals confidence.

**For Context Management**: Separating substantiated from unsubstantiated knowledge graphs provides tractable uncertainty modeling. Rather than treating all memory as equally reliable, systems can reason about confidence and provenance. The pgvector layer suggests hybrid symbolic-neural approaches where embeddings augment rule-based validation.

**For Production Systems**: The 1000-concurrent-user validation and <200ms p95 latency demonstrate practical feasibility. The Kubernetes setup is standard, but the multi-stage conflict detection (rule → embedding → ontology) suggests a pattern for balancing speed vs. precision in validation pipelines.

**Methodological Concern**: The benchmark is proprietary and undisclosed. The 99% result on custom tests doesn't establish generalization—independent validation against standard benchmarks would strengthen claims. The Mem0 comparison (66.9%) is useful but lacks details on test overlap or fair experimental setup.

**Architectural Insight**: The four-judge consensus pattern decouples different validation concerns (safety, semantic validity, temporal logic, aggregation). This could generalize beyond memory to other multi-agent LLM systems requiring coordinated decisions.

## Related

- [Mem0 Memory Platform](https://mem0.ai/) - Current state-of-the-art (~66.9% accuracy) for comparison
- Multi-agent reasoning with consensus mechanisms (relevant for reasoning architectures)
- Grammar-constrained generation via Outlines - enables deterministic validation
- Semantic conflict detection in knowledge graphs
