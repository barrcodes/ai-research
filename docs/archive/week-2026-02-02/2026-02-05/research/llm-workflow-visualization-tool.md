# I Built a Tool to Visualize LLM Workflows as Interactive and Shareable Graphs

| | |
|---|---|
| **Source** | Reddit r/LocalLLaMA |
| **URL** | [old.reddit.com/r/LocalLLaMA/comments/1qw6rwc](https://old.reddit.com/r/LocalLLaMA/comments/1qw6rwc/i_built_a_tool_to_visualize_llm_workflows_as/) |
| **Author** | Cyanosistaken |
| **Researched** | 2026-02-05 |

## Overview

Codag is an open-source VSCode extension that visualizes LLM agent workflows and multi-step pipelines as interactive, shareable graphs directly within your codebase. The tool addresses the cognitive overhead problem of tracking complex agent logic across distributed code by rendering the entire workflow structure as an explorable DAG (directed acyclic graph), with particular emphasis on understanding Vercel's AIChatbot repository structure in the demo.

## Key Points

- **Problem Statement**: As agent codebases grow in complexity, developers quickly lose visibility into the complete workflow structure and call chains. The author explicitly noted that opening LangChain's generated dependency graph crashed their system due to sheer complexity.

- **Multi-Framework Support**: Works across OpenAI, Anthropic, Gemini, LangChain, LangGraph, CrewAI—with language support for Python, TypeScript, Go, Rust, Java, and more. This broad compatibility positions it as a meta-tool applicable across the agentic ecosystem.

- **Interactive Graph Visualization**: Unlike static documentation, the graphs are navigable and shareable, embedded directly in VSCode as an extension rather than requiring separate tools or external visualization services.

- **Architecture**: Open source, available at [https://github.com/michaelzixizhou/codag](https://github.com/michaelzixizhou/codag)

## Technical Details

### What It Solves

The tool's core value proposition is **architectural visibility at scale**. When agents spawn multiple sub-tasks, API calls, tool invocations, and control flows across LangChain, LangGraph, or CrewAI, the resulting code becomes difficult to reason about. A visual representation reveals:

- Execution flow topology (which components execute in sequence vs. parallel)
- Dependencies and data flow between nodes
- Complexity hotspots (dense graph regions indicating coupling)
- Where framework boilerplate dominates vs. domain logic

### Design Approach

- **VSCode-native**: Leverages the VS Code extension ecosystem for tight editor integration—no external tool switching required.
- **Language-agnostic parsing**: Works across multiple programming languages, suggesting the tool performs AST-based analysis of framework-specific patterns (e.g., LangChain `.invoke()` chains, LangGraph graphs, CrewAI task definitions).
- **Interactive exploration**: Full-resolution graphs are explorable (as opposed to static images), allowing developers to zoom, pan, and drill into specific areas.

### Observed Limitations (from feedback)

- LangChain's native structure is "a monstrosity" (quoting community feedback)—the visualization makes this complexity visible, which actually validates the tool's utility for problem identification
- The author attempted a VSCode Chat Participant feature for DAG explanation and lineage analysis but found the VSCode API too limited for reliable implementation

## Implications

### For Practitioners

1. **Workflow Clarity**: As teams adopt agentic architectures, visualization becomes as critical as code review. This tool automates what would otherwise require manual architecture documentation.

2. **Framework Evaluation**: The visualization exposes framework complexity in a quantifiable way. A dense, unnavigable graph is a signal to consider architectural simplification or framework alternatives.

3. **Onboarding & Debugging**: New team members can understand agent logic visually before diving into code. Debugging multi-step workflows becomes tractable when you can see control flow topology.

4. **Integration Readiness**: Works with both proprietary (OpenAI, Anthropic) and open-source (LangChain, LangGraph, CrewAI) frameworks, making it a platform-agnostic investment for teams with mixed tooling.

### Architectural Significance

- **Tool Maturity**: The agentic space has reached a tipping point where developer experience tooling (visualization, debugging, observability) is becoming table-stakes, not a novelty.

- **DAG as First-Class Abstraction**: This positions the workflow DAG itself as a first-class artifact worth visualizing and analyzing, rather than something you piece together from reading code. Expect similar tooling to become standardized.

- **Framework Fragmentation Problem**: The need for a tool that bridges OpenAI, Anthropic, Gemini, LangChain, LangGraph, and CrewAI highlights the lack of a unified workflow abstraction layer in the industry—each framework has its own graph representation, and Codag must handle all of them.

## Discussion Points from Community

- **Feature Request**: DAG lineage/impact analysis (e.g., `codag explain <node>` to show upstream dependencies and downstream impact)—the author noted VSCode API limitations prevented implementation
- **Tooling Standards**: Community questioned why local LLM projects assume Ollama instead of standardizing on the OpenAI API, which is supported by llama.cpp, LM Studio, Kobold.cpp, VLLM, and others
- **Use Cases**: At least one developer is using similar patterns to visualize multi-agent collaboration systems (AI Town fork with mystery-solving gameplay)

## Sources

- [GitHub Repository: michaelzixizhou/codag](https://github.com/michaelzixizhou/codag) - Open source VSCode extension for LLM workflow visualization
