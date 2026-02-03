# Agent Skills

| | |
|---|---|
| **Source** | Agent Skills (agentskills.io) |
| **URL** | [agentskills.io/home](https://agentskills.io/home) |
| **Researched** | 2026-02-03 |

## Overview

Agent Skills is an open format standard for packaging reusable capabilities into portable, version-controlled agents. Developed by Anthropic and adopted across the agentic ecosystem (Claude Code, Cursor, GitHub, VS Code, etc.), it enables agents to discover and load specialized knowledge on demand rather than carrying all context upfront. The format uses **progressive disclosure**—metadata only at startup, full instructions on activation—to minimize token overhead while maximizing capability reuse.

## Key Points

- **Portable capability distribution**: Skills are self-contained directories with a required `SKILL.md` file plus optional `scripts/`, `references/`, and `assets/` folders. This makes them easy to version, share, and integrate across different agent products without reimplementation.

- **Progressive disclosure architecture**: Agents load only skill metadata (~50-100 tokens per skill: name + description) at startup. Full instructions load only when a task matches the skill. Referenced files and scripts load on demand. This pattern solves a critical agentic problem: scaling capability access without exploding context window usage.

- **Two integration paths**: Filesystem-based agents (stronger capability) directly execute shell commands to load SKILL.md and access bundled resources. Tool-based agents use agent-defined tools to trigger skills and fetch assets. This flexibility allows adoption across diverse agent environments.

- **Strong naming and validation conventions**: Skill names are restricted to lowercase alphanumeric + hyphens, validated via frontmatter requirements and tooling. The `allowed-tools` field (experimental) provides a foundation for deterministic tool authorization within skills. A reference library (`skills-ref`) validates skills and generates context injection XML.

## Technical Details

**SKILL.md Format:**
```yaml
---
name: pdf-processing
description: Extract text and tables from PDFs, fill forms, merge documents.
license: Apache-2.0
metadata:
  author: example-org
  version: "1.0"
allowed-tools: Bash(pdfplumber:*) Read
---

# Markdown instructions follow
```

**Discovery & Injection:**
Agents scan configured directories for valid `SKILL.md` files, parse frontmatter only, then inject available skills into system prompt as XML:

```xml
<available_skills>
  <skill>
    <name>pdf-processing</name>
    <description>...</description>
    <location>/absolute/path/to/skill/SKILL.md</location>
  </skill>
</available_skills>
```

**Context Efficiency:**
- Metadata: ~100 tokens per skill (name + description)
- Full instructions: < 5000 tokens recommended, split longer content into referenced files
- Resources: On-demand loading keeps the working context tight

**Security Model:**
The spec acknowledges script execution risks and recommends sandboxing, allowlisting (via `allowed-tools`), confirmation prompts, and audit logging. No enforcement mechanism is built into the format itself—implementation details are delegated to agent platforms.

## Implications

**For practitioners building agentic systems:**

1. **Skill as organizational knowledge unit**: This format treats skills as the minimal atomic capsule for capturing organizational workflows, specialized processes, or domain expertise. Version control each skill independently rather than bundling capabilities into monolithic agent images.

2. **Interoperability as competitive advantage**: Skills are intentionally format-agnostic to environment. A well-written skill works across Claude Code, Cursor, GitHub Copilot, and proprietary agent stacks. This reduces lock-in and lets teams standardize on skill libraries rather than agent platforms.

3. **Context efficiency becomes architectural constraint**: Progressive disclosure forces early-stage design decisions: what metadata is necessary for discovery vs. what can load later? This mirrors microservice API contracts—skills must be "discoverable by description" or they won't be selected. Poor descriptions sabotage skill adoption.

4. **Security is delegated, not centralized**: No skill has built-in enforcement. The `allowed-tools` field is experimental and optional. Teams must implement execution policies in their agent runtime, not in skill definitions. This is a trade-off: flexibility vs. centralized control.

5. **Migration path for legacy systems**: Organizations with mature runbook/workflow libraries can incrementally package existing procedures as skills without rewriting the underlying logic. Shell-based agents can cat files; tool-based agents get custom loaders.

**Adoption signals:** 25+ major tool integrations (Anthropic, OpenAI, Databricks, Spring AI, etc.) indicate this is becoming infrastructure, not just a library. The open governance model and GitHub-based development reduce vendor lock-in concerns.

## Sources

- [agentskills.io/home](https://agentskills.io/home) - Overview and adoption
- [agentskills.io/what-are-skills](https://agentskills.io/what-are-skills) - Core concepts and design patterns
- [agentskills.io/specification](https://agentskills.io/specification) - Complete format specification
- [agentskills.io/integrate-skills](https://agentskills.io/integrate-skills) - Integration architecture and patterns
