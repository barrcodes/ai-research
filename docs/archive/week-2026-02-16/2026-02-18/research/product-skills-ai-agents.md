# Product Skills for AI Agents

| | |
|---|---|
| **Source** | GitHub Repository (assimovt/productskills) |
| **URL** | [github.com/assimovt/productskills](https://github.com/assimovt/productskills) |
| **Researched** | 2026-02-18 |

## Overview

Product Skills is a modular framework of markdown-based instructions that encode established product management methodologies into structured AI agent prompts. Rather than building bespoke agents, the system packages proven frameworks (Shape Up, Obviously Awesome, Mom Test, Teresa Torres) as reusable skills that integrate with any LLM-based coding assistant, enabling product managers and founders to accelerate discovery, strategy, prioritization, and documentation workflows.

## Key Points

- **Modular architecture**: 16 discrete skills organized into five functional categories (Discovery, Strategy, Prioritization, PRD Writing, Launch & Measurement) allow selective adoption and composition
- **Framework-agnostic delivery**: Skills are plain markdown files, making them portable across Claude Code, Cursor, Codex, Devin, or any LLM interface without special integration
- **Intentionally concise**: 50-150 lines per skill balances actionable specificity with usability—avoiding both oversimplification and cognitive overload
- **Workflow integration pattern**: Skills work best when chained (e.g., run discovery → strategy → prioritization in sequence) rather than as isolated tools

## Technical Details

**Skill categories:**

| Category | Count | Examples |
|---|---|---|
| Discovery & Research | 5 | User interviews, problem validation, JTBD, research synthesis |
| Strategy & Positioning | 3 | Competitor analysis, positioning, strategy docs |
| Prioritization & Scoping | 3 | Feature ranking, scope reduction, bet sizing |
| PRD Writing | 1 | Evidence-driven documentation |
| Launch & Measurement | 4 | Launch planning, metrics frameworks, experimentation |

**Integration points:**
- Claude Code: Direct import via CLI or plugin
- Cursor: Copy to `.cursor/rules/` for context-aware agent behavior
- Standard LLM usage: Paste markdown instructions into any chat interface

## Implications

This pattern is architecturally significant because it **separates domain expertise from agent infrastructure**. Rather than building custom agents, teams codify their methodologies once and reuse them across multiple tools and team members.

**For practitioners:**
- **When to use**: Teams lacking formalized product processes or seeking to accelerate repetitive work (research synthesis, strategy documentation)
- **Trade-offs**: Markdown-based skills require human judgment to apply effectively—they're enhancers, not replacements for decision-making
- **Adoption strategy**: Start with one category (e.g., Prioritization), validate workflow gains, then expand
- **Alternative approaches**: Purpose-built agents (more polished but tool-locked), custom LLM fine-tuning (higher upfront cost, better long-term specificity)

This demonstrates a broader pattern: **best practices are portable**. Encoding them as prompts rather than code makes them shareable, versioned, and immediately useful across the AI tooling ecosystem.

## Sources

- [github.com/assimovt/productskills](https://github.com/assimovt/productskills) - Modular AI agent skills for product management frameworks
