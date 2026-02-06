# Snapchat's GiGL: Production Graph Neural Networks at Billion-Scale

| | |
|---|---|
| **Source** | r/MachineLearning |
| **URL** | [old.reddit.com/r/MachineLearning/comments/1qxcfmt/](https://old.reddit.com/r/MachineLearning/comments/1qxcfmt/r_snapchats_recommendation_system_had_a_scaling/) |
| **Researched** | 2026-02-06 |

## Overview

Snapchat's GiGL (Gigantic Graph Learning) framework bridges the production gap that prevents most GNN research from scaling to real-world constraints: processing 900M nodes, 16.8B edges, and 35+ production launches across friend/content/ads recommendation in under 12 hours daily. Rather than innovating at the model layer, GiGL's architecture restructures GNN pipelines as orchestrated ETL systems, reducing costs 80% versus previous Beam implementations while demonstrating that graph quality and retrieval strategy trump model complexity.

## Key Points

- **Scale reality**: 100B edges alone require 800GB for integer IDs before any features; practical GNN scaling is primarily an infrastructure problem, not a modeling problem

- **Tabularization strategy**: Pre-computing k-hop neighborhoods to cloud storage transforms distributed graph sampling from real-time traversal into standard data-parallel ML training, eliminating the need for distributed graph state during training

- **Six-stage pipeline**: Config resolution → distributed preprocessing (Beam/BigQuery) → Spark-based subgraph sampling → split generation with leakage masking → PyTorch DDP training → parallel CPU inference, each component independently horizontally scalable

- **Graph > Model**: Single feature normalization improved MRR 0.39→0.54 (38% lift); switching from friendship graph to engagement graph yielded +8.9% without any model change

- **Attention dominance**: GAT consistently outperforms sum/mean aggregation on social graphs (scale-free degree distributions mean unequal neighbor importance); +6.5% improvement over GraphSAGE

- **Query-time insights**: Using existing friends' embeddings for ANN queries ("Stochastic EBR") yielded +10.2% to +13.9% on business metrics—no retraining required, purely a retrieval strategy change

- **Multi-domain applicability**: Friend recommendation (user-user engagement), content/Discover (user-video bipartite + co-engagement), ads (product co-engagement)—all benefited from GNN embeddings primarily in retrieval and as auxiliary ranker features

## Technical Details

### Architecture Constraints Addressed

The core insight is that PyG's existing capabilities (NeighborLoader, ClusterLoader, FeatureStore, DDP) handle graphs with billions of edges on capable infrastructure, but production systems need:

1. **Deterministic config management** - frozen URIs enable idempotency and retrieval optimization (rerun trainer 50 times without resampling)
2. **Distributed enumeration** - contiguous integer node IDs from source relational IDs (BigQuery)
3. **Leakage prevention** - masking validation/test edges during train split generation
4. **Retrieval separation** - training produces embeddings; inference is CPU-parallel across workers

### Scaling Trade-offs

**Tabularization approach**:
- Precomputes subgraph neighborhoods (no shared graph state during training)
- Trades real-time expressiveness for cost efficiency (80% reduction vs. Beam)
- Enables standard data-parallel training without distributed graph engines
- Requires offline batch cycles; incompatible with online/streaming workloads

**Component selection**:
- Beam/Dataflow for general ETL (flexible, managed)
- Spark for large-scale sampling operations (joins on edge lists)
- NebulaGraph backend option for heterogeneous graphs (homogeneous = pure ETL)
- Kubeflow or Vertex AI orchestration (GCP-native stack)

### Experimental Validation

35+ production launches across two years validated:
- GraphSAGE baseline: +10% new friends made
- Engagement graph swap: +8.9% additional (same model, better graph definition)
- GAT adoption: +6.5% over mean aggregation
- Stochastic EBR retrieval: +10.2%–+13.9% on core metrics
- Ads domain: 27.6% recall improvement with 10% training data vs. shallow embeddings (precision parity)

All metrics include directional improvements; no reported regressions.

## Implications

### Architectural Decisions

1. **GiGL is not PyG replacement but orchestration layer** - You still write standard PyG models; GiGL handles pipeline infrastructure. This design choice forces separation of concerns but requires GCP infrastructure adoption.

2. **Graph engineering is the leverage point** - Feature normalization, graph definition (friends vs. engagement), and node/edge feature selection delivered larger empirical gains than architectural novelty. Teams should invest heavily in data quality before iterating on layer types.

3. **Retrieval strategy as first-class concern** - Query-time decisions (EBR with friend embeddings) can match model improvements without retraining. Ranking systems should treat embedding usage and retrieval strategy as tunable parameters alongside model architecture.

4. **Cost-driven design decisions** - 80% cost reduction through tabularization (vs. Beam) is architecturally significant. For teams at smaller scales (< billions of edges), PyG's distributed training primitives or standalone solutions like GraphStorm may be more cost-effective than building bespoke infrastructure.

### Production Readiness Criteria

GiGL makes sense when:
- Graph has **billions of edges** (< 1B: standalone PyG may suffice)
- **Daily batch inference** acceptable (not real-time)
- **GCP infrastructure** commitment (Dataflow, Dataproc, Vertex AI, BigQuery)
- **35+ production launches expected** (amortizes framework engineering)

GraphStorm (AWS) and standalone PyG are viable alternatives for different infrastructure or iteration-speed constraints.

### Training Data Implications

Snapchat's ads finding (27.6% recall gain with 10% training data) suggests GNN embeddings may have better label efficiency than shallow embeddings. This aligns with structural learning advantages but warrants validation on your specific graph topology.

## Sources

- [GiGL: Large-Scale Graph Neural Networks at Snapchat](https://arxiv.org/pdf/2502.15054) - Original research paper with experimental details
- [GiGL GitHub Repository](https://github.com/Snapchat/GiGL/tree/main) - Open-source framework
- [GiGL Architecture Documentation](https://snapchat.github.io/GiGL/docs/user_guide/overview/architecture.html) - Official reference
- [PyTorch Geometric (PyG)](https://github.com/pyg-team/pytorch_geometric) - Underlying GNN library
