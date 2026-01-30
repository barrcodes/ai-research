# BrainPro: Secure Agentic Platform in Rust

| | |
|---|---|
| **Source** | GitHub (jgarzik/brainpro) |
| **URL** | [github.com/jgarzik/brainpro](https://github.com/jgarzik/brainpro) |
| **Researched** | 2026-01-29 |

## Overview

BrainPro is a vendor-neutral local agentic coding assistant that routes tasks to multiple LLM providers while maintaining granular security boundaries. Rather than lock into single-model dependencies, it enforces project-scoped file access, permission-based tool restrictions, and audit logging—addressing the core tension between agent autonomy and operational safety in multi-model environments.

## Key Points

- **Multi-model routing**: Different task types dispatch to specialized models (Claude for coding, Qwen for planning, GPT for exploration) rather than forcing all work through one provider
- **Dual execution paths**: CLI-based direct mode (`yo` with MrCode persona, 7 tools) for local development; gateway mode with daemon deployment for remote team collaboration
- **Fine-grained permission model**: Rules-based access control (allow/ask/deny) at the tool level, not just at the agent level
- **Subagent delegation**: Structured agent composition with tool restrictions—skilled agents inherit only necessary capabilities
- **Skill packs**: Reusable instruction sets with bound tool access, treating agent behavior as composable, restricted primitives
- **Audit transparency**: JSONL session transcripts and execution logs for security review and compliance

## Technical Details

BrainPro decouples agent capabilities from model selection. The architecture offers two deployment patterns:

| Aspect | Direct Mode | Gateway Mode |
|--------|------------|--------------|
| **Interface** | CLI (`yo`) | `brainpro-gateway` + `brainpro-agent` |
| **Persona** | MrCode (7 tools) | MrBot (12+ tools) |
| **Execution** | Local machine | Remote/containerized |
| **Team scope** | Single developer | Multi-developer |
| **MCP support** | Yes | Yes |

Tool set includes Read, Write, Edit, Grep, Glob, Bash, and Search with external tool integration via Model Context Protocol. Execution remains project-scoped—no cross-directory access by default.

## Implications

**For architecture**: This approach decouples LLM selection from agentic workflows. Organizations no longer need to architect around provider lock-in; you can swap models without rewriting permission logic.

**For security**: Permission boundaries move from the agent level to the tool level, preventing privilege escalation through agent composition. The ask/deny rules force human-in-the-loop decisions on sensitive operations without blocking the entire agent.

**For team scaling**: Skill packs enable governance at organizational level—define restricted instruction templates once, deploy consistently across teams. Subagent delegation lets senior developers build multi-step workflows without each agent inheriting full permissions.

**When to adopt**: Best suited for teams that work across multiple LLM providers, need compliance audit trails, or want to delegate to specialized agentic components without blanket access grants. Less critical if you're standardized on a single provider and trust that provider's safety guarantees.

## Related

- [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) – External tool integration standard
- [Agent security patterns](https://github.com/anthropics/anthropic-sdk-python) – SDK agent implementations
