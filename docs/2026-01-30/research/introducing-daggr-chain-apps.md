# Introducing Daggr: Chain Apps Programmatically, Inspect Visually

| | |
|---|---|
| **Source** | Hugging Face Blog |
| **URL** | [huggingface.co/blog/daggr](https://huggingface.co/blog/daggr) |
| **Researched** | 2026-01-30 |

## Overview

Daggr is a Python-first orchestration library for AI workflows that solves the code-vs-GUI dilemma in pipeline construction. It lets you define pipelines as version-controllable code while automatically generating interactive visual canvases for debugging. The tool natively integrates Gradio Spaces, custom functions, and inference models into unified workflows with zero boilerplate.

## Key Points

- **Code-first with auto-generated visualization** — Define workflows in Python; the canvas is generated automatically. This means Git-friendly, diff-able pipelines unlike node-editor GUIs.

- **Interactive debugging without re-running** — Inspect intermediate outputs, modify inputs, and rerun individual nodes. Game-changer for complex pipelines where a single iteration cost is high.

- **Seamless Gradio Space composition** — Use public/private Spaces as first-class workflow nodes. No adapters, no API boilerplate—just reference the Space name.

- **Three pluggable node types** — `GradioNode` (Spaces), `FnNode` (custom Python), `InferenceNode` (HF inference providers). Swap providers via "backup nodes" for resilience.

- **Built-in state persistence** — Workflows auto-save intermediate results, canvas state, and inputs. "Sheets" feature lets you maintain multiple workspaces.

## Technical Details

**Architecture:** DAG-based with lazy evaluation. Nodes track dependencies; outputs from upstream nodes flow as inputs to downstream nodes.

**Example - Image to 3D Pipeline:**
```python
from daggr import GradioNode, FnNode, InferenceNode, Graph

# Remove background (Gradio Space)
bg_remover = GradioNode("hf-applications/background-removal", api_name="/image")

# Downscale (custom function)
downscaler = FnNode(downscale_fn, inputs={"image": bg_remover.final_image})

# Enhance (inference provider)
flux = InferenceNode("black-forest-labs/FLUX.2-klein-4B:fal-ai",
                     inputs={"image": downscaler.image})

graph = Graph(name="Image to 3D", nodes=[bg_remover, downscaler, flux])
graph.launch()
```

**Deployment:** Single command—`graph.launch()` for local, `graph.launch(share=True)` for ephemeral public links. Works directly in Hugging Face Spaces via `requirements.txt`.

## Implications

**Trade-offs:**
- Beta status means API surface may shift—use for experimentation, not mission-critical production yet.
- Data persistence is local; workflow state loss is possible during library updates.
- Heavier than shell scripts, lighter than Airflow/Prefect—targets the exploration → prototyping gap.

**When to use:**
- Multi-step AI workflows where iteration speed matters (e.g., image processing chains, LLM-based data pipelines).
- Rapid prototyping with mixed sources (existing Spaces + custom logic).
- Cross-functional collaboration where non-engineers need visibility into pipeline execution.

**Architectural significance:**
- Bridges the "declarative code vs. interactive UI" tension that's plagued ML tools. Version control + visual inspection were previously mutually exclusive.
- Gradio integration is strategic—shifts Spaces from isolated demos to composable workflow primitives.

## Related
- [Gradio](https://www.gradio.app/) - Underlying UI/Space framework
- [Hugging Face Spaces](https://huggingface.co/spaces) - Deployment target and node source
- [GitHub: Daggr](https://github.com/gradio-app/daggr) - Source code and issue tracker
