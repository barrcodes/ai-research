# Tracing Claude Code's LLM Traffic

| | |
|---|---|
| **Source** | George Sung Substack |
| **URL** | [georgesung.substack.com/p/tracing-claude-codes-llm-traffic](https://georgesung.substack.com/p/tracing-claude-codes-llm-traffic) |
| **Researched** | 2026-01-26 |

## Overview

Claude Code implements a sophisticated multi-tier LLM architecture with parallel processing, cache optimization, and controlled sub-agent delegation. The system stratifies model selection by task type—lightweight models (Haiku 4.5) handle metadata and exploratory tasks while heavyweight Opus powers the main reasoning loop. This design substantially reduces cost and latency while maintaining reasoning capability where it matters.

## Key Points

- **Dual-model optimization**: Lightweight Haiku 4.5 handles metadata generation, codebase exploration, and analysis tasks; Opus 4.5 powers the main agent. Parallel execution with cache pre-filling via dummy requests reduces end-to-end latency.
- **Agentic loop structure**: Metadata generation → main agent reasoning → streaming tool execution → feedback cycles → output summarization → prompt suggestions. Tool results feed back via tool_use_id matching in message history.
- **Sub-agent control architecture**: Task tool spawns specialized sub-agents with restricted capability sets. The Explore sub-agent receives read-only permissions; control returns to parent via embedded agentId for potential resumption.
- **Tool stratification**: Main agent has full access (Glob, Grep, Read, Edit, Write, Bash, Task). Sub-agents receive filtered tool lists based on role. Unique tool_use_id tracking enables reliable multi-turn coordination.
- **Prompt constraint enforcement**: System prompts prohibit specific behaviors (e.g., "STRICTLY PROHIBITED" from modifications for explorers). Project-level instructions (CLAUDE.md) auto-injected into user messages.

## Technical Details

**Cache Strategy**: Dummy requests with `max_tokens=1` pre-fill the KV cache for subsequent heavyweight LLM calls, enabling parallel tool execution without sequential wait penalties.

**Tool Execution Pattern**:
- Parallel tool calls (ls, grep, read operations simultaneously)
- Tool results embed matching tool_use_id for session continuity
- Sub-agent results wrapped in user messages with agentId for resumption

**Prompt Structure**:
- Main agent: Professional tone, no time estimates, references CLAUDE.md instructions
- Sub-agents: Capability restrictions via system prompt (read-only for explorers)
- User prompts: Auto-inject project-specific context automatically

**Model Selection Logic**:
- Haiku 4.5: Metadata, codebase exploration, analysis, summarization
- Opus 4.5: Main reasoning, complex decision-making, code generation

## Implications

**Cost-Efficiency Trade-off**: Offloading 30-40% of tasks to lightweight models dramatically reduces token costs without sacrificing quality on reasoning-heavy operations. Architects should identify similar stratification opportunities in their own agentic systems.

**Capability vs. Permission Model**: Rather than model-level restrictions, Claude Code uses tool-level filtering. Sub-agents remain powerful but are constrained by available actions. This is operationally simpler than maintaining separate model configurations.

**Resumption Design**: The agentId embedding pattern suggests Claude Code supports agent resumption—critical for long-running workflows that may be interrupted or need to retry from a checkpoint.

**Parallel Execution**: The dummy-request cache technique is language-agnostic and applicable to any system needing synchronous execution of multiple independent tool calls without sequential ordering penalties.

## Related

- [Claude Code Official Documentation](https://docs.anthropic.com/) - API and tool specifications
- [LLM Caching and KV Cache Pre-filling](https://docs.anthropic.com/) - Performance optimization techniques
