# Predicting Edge Importance in GPT-2's Induction Circuit from Weights Alone

| | |
|---|---|
| **Source** | r/MachineLearning |
| **URL** | [old.reddit.com/r/MachineLearning/comments/1r94poz/](https://old.reddit.com/r/MachineLearning/comments/1r94poz/r_predicting_edge_importance_in_gpt2s_induction/) |
| **Paper** | [zenodo.org/records/18686231](https://zenodo.org/records/18686231) |
| **Researched** | 2026-02-20 |

## Overview

A novel weight-structure-based scoring method predicts causal edge importance in transformer circuits without forward passes or ablations, achieving Spearman ρ=0.623 on GPT-2 small's induction circuit at 125x speedup over path patching. The "Cheap Anchor" framework originated from algebraic number theory (non-unique factorization rings) and generalizes structural properties—spectral concentration and downstream path weight—that predict functional significance in coupled systems. This work demonstrates that transformer weight matrices encode functional roles in their structure, accessible via linear-algebraic analysis independent of activation dynamics.

## Key Points

- **Two weight-based properties predict causal importance**: Spectral concentration (singular value distribution) and downstream path weight of virtual weight matrices achieve ρ=0.623 (p < 10⁻⁷) correlation with path patching ground truth, substantially outperforming weight magnitude (ρ=0.070) and gradient attribution (ρ=−0.262)

- **Massive computational advantage**: Scoring all edges in GPT-2 small's induction circuit takes 2 seconds (125x faster than 250-second path patching), making edge importance estimation feasible for larger models and denser circuits where causal interventions become prohibitively expensive

- **Explains ~39% of variance**: The structural properties capture only the linear-algebraic component of edge importance; the remaining 61% depends on activation-dependent nonlinearities (attention softmax, LayerNorm, MLP activations) not captured by virtual weight matrix analysis

- **Explicitly scoped to known circuits**: This is not circuit discovery—the induction circuit was identified first from attention patterns, then edges within that subgraph were scored. The global test (whether high-scoring edges cluster around known circuits across all 21,750 GPT-2 small edges) remains future work

- **Method originates from unexpected domain**: The scoring approach emerged from studying factorization dynamics in imaginary quadratic integer rings using TAME (Technological Approach to Mind Everywhere) framework, suggesting structural properties governing local constraint satisfaction have deep cross-domain generality

## Technical Details

**Cheap Anchor Scoring Method**

The framework identifies four structural properties that predict importance:
1. **Discrimination**: Edge selectivity in distinguishing different constraint configurations
2. **Non-redundancy**: Degree to which the edge's function is not duplicated elsewhere
3. **Signal flow**: How much informational capacity the edge pathway possesses
4. **Cascade depth**: Position in the dependency hierarchy of the circuit

For transformers, two TAME properties transferred successfully to the induction circuit:
- **Spectral concentration** of virtual weight matrices (singular value distribution skewness)
- **Downstream path weight** accumulated across cascading layers

**Virtual Weight Matrix Abstraction**

Transformers are linearized by composing weight matrices along paths from source to sink components, collapsing intermediate nonlinearities. This lossy abstraction explains the modest explained variance—it captures what the linear pathway *could* transmit, not what the full computation actually transmits. Two other properties tested (signal flow and cascade depth in their original formulations) failed to transfer to transformer residual stream architecture, suggesting the abstraction boundaries matter.

**Empirical Results**

- Prior work on feedforward networks: ρ=0.836–0.931 across scales from 80 to 3,120 edges, 18–597x speedup over ablation
- GPT-2 small induction circuit: ρ=0.623 on 63 edges, 2-second runtime vs. 250 seconds for path patching
- Baseline comparisons show weight magnitude and gradient-based attribution are poor proxies for causal importance in this circuit
- Ground truth: path patching with logit lens analysis to measure causal effect on induction output

## Implications

**For circuit analysis practice**: This method enables coarse triage before expensive causal interventions. Practitioners can score all edges in a circuit cheaply, then path-patch only the top 5% to obtain precise importance rankings. For large models, this pre-filtering could reduce compute budgets by orders of magnitude.

**For mechanistic interpretability theory**: The predictiveness of spectral concentration suggests transformer weight matrices are not arbitrary parameterizations—they carry detectable structural signatures of functional role that are accessible without probing activations. This contradicts views treating weights as opaque optimization artifacts and suggests a partially legible layer exists between parameter space and function space.

**Generalization remains uncertain**: Success on induction heads (clean, well-studied, structured computation) is necessary but not sufficient. Messier circuits like factual recall or reasoning involve distributed computation where edge-level analysis may be uninformative. The correlation is moderate (39% explained variance), useful for ranking but not precise importance scoring. Single model, single circuit—replication is required.

**Activation nonlinearities dominate remaining variance**: The 61% of unexplained variance likely comes from how attention softmax, LayerNorm scaling, and MLP gating interact with linear pathways. Weight structure captures signal capacity; activation states determine actual signal flow. This suggests pure weight-based analysis has a theoretical ceiling around 40-50% variance explained unless nonlinearity patterns can be predicted structurally.

**Practical limitations of virtual weight matrices**: The linearization ignores that attention masks depend on input, LayerNorm scales dynamically, and MLP activations are input-dependent. For circuits heavily dependent on data-dependent gating, this abstraction breaks down. The method works best for circuits where computation is relatively input-invariant (good match for induction heads, worse for circuits requiring dynamic routing).

## Next Steps (Author's Plan)

1. **Global percentile test**: Score all ~21,750 edges in GPT-2 small and verify whether the 63 ground-truth induction edges cluster in top percentiles
2. **Scale validation**: Test on GPT-2 medium/large where speedup advantage widens and practical utility becomes clearer
3. **Cross-circuit testing**: Indirect object identification, factual recall—messier circuits are the true test of generality
4. **Code release**: GitHub repo with TransformerLens integration for reproducibility under 5 minutes on consumer GPU

## Sources

- [Reddit post: Predicting Edge Importance in GPT-2's Induction Circuit](https://old.reddit.com/r/MachineLearning/comments/1r94poz/r_predicting_edge_importance_in_gpt2s_induction/) - Original research announcement with detailed methodology and limitations
- [Zenodo: From Number Theory to Neural Networks: The Anchor Edge Framework](https://zenodo.org/records/18686231) - Full paper with cross-domain validation and technical specifications
