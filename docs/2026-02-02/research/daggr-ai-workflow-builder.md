# Introducing Daggr: Chain apps programmatically, inspect visually

| | |
|---|---|
| **Source** | Hugging Face Blog |
| **URL** | [huggingface.co/blog/daggr](https://huggingface.co/blog/daggr) |
| **Researched** | 2026-02-02 |

## Overview

Daggr is an open-source Python orchestration library that bridges code-first workflow definition with automatic visual debugging. It chains Gradio apps, ML models, and custom functions into inspectable pipelines, enabling node-level debugging without full re-execution—addressing a critical gap between fragile scripts and heavyweight platforms like Airflow.

## Key Points

- **Three-tier node system**: GradioNode (Space APIs), FnNode (custom Python), InferenceNode (HF inference endpoints) compose into directed graphs with automatic state persistence
- **Interactive debugging model**: Inspect any node's output, modify inputs, and rerun individual steps without re-executing upstream—critical for expensive inference pipelines
- **Code-driven with visualization**: Define workflows in Python (git-friendly), Daggr auto-generates visual canvas—avoids lock-in typical of GUI builders while retaining inspectability
- **Native Gradio integration**: First-class support for Spaces with `run_locally=True` fallback (clone/execute locally if remote fails)
- **Minimal API surface**: Intentionally lightweight with simple `.launch()` deployment to Spaces or locally

## Technical Details

**Node Composition Model**
```python
# Chained via output reference
image_node = GradioNode("hf-applications/Z-Image-Turbo", ...)
cleaner = GradioNode("hf-applications/background-removal",
                     inputs={"image": image_node.image})
graph = Graph(nodes=[image_node, cleaner])
```

**State Architecture**: Automatic persistence across "sheets" (workspaces) with cached results enables resumable workflows and side-effect-free reruns of individual nodes. This is architecturally significant—downstream nodes don't recompute when you adjust an upstream parameter.

**Execution Model**: Local Gradio server integration (Python 3.10+) with optional remote fallback. Lightweight, suitable for rapid prototyping rather than high-concurrency production systems.

## Implications

**When to use**: Ideal for ML engineers building demos, experimental pipelines, or complex inference chains requiring interactive debugging. Strong fit for rapid iteration on model combinations.

**Trade-offs**:
- Lightweight design (beta-stage API) means less battle-hardened than Airflow/Prefect for production orchestration
- Gradio-first ecosystem assumes comfort with that constraint; not a general-purpose DAG engine
- State persistence is local/session-based, not suitable for distributed team workflows or cloud deployments requiring audit trails

**Architectural implications**: The node-level caching pattern addresses a real pain point in ML workflows—most orchestrators force full re-execution when parameters change. Daggr's automatic inspection without re-execution could influence how teams prototype multi-model inference chains.

**Actionable**: Consider for rapid multi-model experiment spaces, internal dashboards, or customer-facing demos where iteration speed matters more than distributed reliability. Pair with Spaces for zero-deployment effort.

## Sources

- [Hugging Face Blog: Introducing Daggr](https://huggingface.co/blog/daggr) - Official announcement with examples and architecture overview
