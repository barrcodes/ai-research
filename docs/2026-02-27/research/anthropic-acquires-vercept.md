# Anthropic Acquires Vercept to Advance Claude's Computer Use Capabilities

| | |
|---|---|
| **Source** | Anthropic |
| **URL** | [anthropic.com/news/acquires-vercept](https://www.anthropic.com/news/acquires-vercept) |
| **Researched** | 2026-02-27 |

## Overview

Anthropic acquired Vercept, a startup specializing in vision-based AI interaction with standard software interfaces, to accelerate Claude's autonomous task completion capabilities. The acquisition consolidates expertise in perception and interaction—core bottlenecks in agentic AI systems—with dramatic performance gains already demonstrated on real-world benchmarks.

## Key Points

- **Vision + Interaction as foundational**: Vercept solved the hard perception problem of enabling AI to "see and act within the same software humans use every day," moving beyond code-only automation
- **Benchmark acceleration**: OSWorld performance jumped from <15% (late 2024) to 72.5% (current), suggesting rapid progress on multi-step task completion across live applications
- **Full integration, not acquisition**: Vercept's external product winds down; team joins Anthropic to focus exclusively on advancing Claude's computer use capabilities
- **Human-parity approach on key tasks**: Sonnet 4.6 now approaches human-level performance on practical workflows like spreadsheet navigation and multi-tab web form completion

## Technical Details

**Core Capability Gap Addressed**: Vercept's founding principle—that "making AI genuinely useful requires solving hard perception and interaction problems"—directly aligns with Anthropic's observed bottleneck in agentic systems. Most LLM automation tools handle either perception (vision) *or* interaction (control), but not both effectively in real-time with live UIs.

**Benchmark Significance**: The OSWorld jump (15% → 72.5%) indicates fundamental improvements in:
- Visual understanding of dynamic interfaces
- State tracking across multi-step workflows
- Robust interaction with unpredictable UI layouts and behavior

**Architectural Implication**: This is not a feature add-on but a capability that restructures how Claude approaches task decomposition. Instead of generating code for isolated steps, Claude can now navigate stateful applications where real-time feedback shapes each subsequent action.

## Implications

**For Practitioners Building Agentic Systems**:
- This acquisition signals that vision + interaction are becoming table-stakes for autonomous agents, not optional polish
- Companies relying on code-generation-only approaches face architectural debt as Claude's UI-native capabilities grow
- Tool use (via function calls) may become secondary to direct interface manipulation for many workflows

**Decision Point**: When building agents, evaluate whether your system can remain code-only or if you need vision-based perception + interaction layers like Vercept's. The 4.6x performance improvement suggests the vision path scales significantly better on real-world tasks.

**Competitive Angle**: Other LLM providers without equivalent vision + interaction capabilities will struggle to match Claude's performance on human-facing workflows (web automation, enterprise software, data entry). This is a structural advantage, not a model parameter difference.

**When to Use**: Computer-use-capable Claude for:
- Multi-application workflows requiring state awareness (spreadsheets across browser tabs)
- Legacy software without APIs
- High-friction data entry and form completion
- Workflows where visual context changes per action

## Sources

- [Anthropic News: Acquires Vercept](https://www.anthropic.com/news/acquires-vercept) - Official acquisition announcement with technical details and performance metrics
