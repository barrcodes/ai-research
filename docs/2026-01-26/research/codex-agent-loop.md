---
title: Unrolling the Codex agent loop
source: OpenAI
url: https://openai.com/index/unrolling-the-codex-agent-loop/
researched_at: 2026-01-26T00:00:00Z
fetch_method: web-search
---

# Unrolling the Codex Agent Loop

## Overview

Michael Bolin's article on OpenAI's engineering blog details the core Codex CLI agent loop architecture, providing practical patterns and best practices for agentic systems. The Codex agent loop exemplifies a production-grade implementation of iterative agent-model-tool interaction, addressing context management, tool execution, and execution termination strategies that generalize across agent frameworks.

## Key Points

- **Iterative Loop Architecture**: User task → context preparation → model inference → response handling → tool execution → context update → repeat until completion
- **Context Expansion Strategy**: Agent loop grows context with each iteration (tool outputs, logs, diffs, test results accumulate), requiring proactive context management
- **Tool Execution Model**: Agent orchestrates tool invocations (hundreds possible per round) in isolated execution containers with structured output capture
- **Execution Termination**: Loop continues until model generates assistant message (not tool invocation) or hits maximum step limit
- **Token Window Management**: Context compression required when exceeding threshold; original conversation replaced with condensed representation preserving intent and history

## Technical Details

### Agent Loop Execution Pattern

The core pattern implements these sequential phases:

1. **Prepare Context**: Combine task specification + system instructions + conversation history + agent memory into structured prompt
2. **Call Model**: Send context to LLM, receive response with potential tool call directives
3. **Handle Response**: Distinguish between two cases:
   - Text content → agent message signals loop termination
   - Tool call directives → proceed to execution phase
4. **Execute Tools**: Run tool invocations in isolated container, capture all output (stdout, stderr, diffs, test results)
5. **Iterate**: Append tool results to context history, return to step 2

### Context Management Strategy

The agent must maintain awareness of context window limits (token capacity bounds vary by model). The compression strategy addresses this through conversation summarization:

- Monitor token consumption throughout conversation
- When exceeding compression threshold, trigger context reduction
- Replace verbose conversation history with condensed summary containing essential information
- Preserve task intent, decision points, and relevant outputs
- Continue inference with reduced context footprint

### Orchestration Architecture

The Codex implementation separates concerns:

- **Prompt Constructor**: Assembles multi-role prompt (system, developer, user) incorporating accumulated context
- **Inference Engine**: Calls LLM with constructed prompt, parses response for tool directives
- **Tool Orchestrator**: Manages isolated execution containers, captures structured output
- **Context Manager**: Tracks token consumption, triggers compression, maintains conversation state

## Implications

**For Practitioners Building Agents:**

1. **Context is the Bottleneck**: Token window management is not optional; expect to implement compression strategies early. Budget context allocation across task definition, history, and results.

2. **Tool Output Accumulation**: Every tool execution adds to context. Design tool outputs to be information-dense and summarizable. Consider structured output formats (diffs, deltas) over full logs.

3. **Iterative Refinement**: The loop pattern enables agents to fail and recover gracefully—tool outputs inform subsequent model calls. Instrument for debugging by preserving full conversation history offline.

4. **Execution Guarantees**: Isolation (sandboxed containers) is critical for production reliability. Model-directed tool invocation requires robust parsing and error handling for malformed directives.

5. **Generalization**: This pattern applies beyond code-generation agents. Any agentic system requiring iterative tool interaction (research, data analysis, system administration) follows this same topology.

6. **Scalability Trade-offs**: Hundreds of tool calls per round possible in principle, but context expansion limits practical parallelism. Sequential execution with smart batching preferred.

## Related

- [Designing agentic loops](https://simonwillison.net/2025/Sep/30/designing-agentic-loops/) - Simon Willison's analysis of agentic loop design patterns
- [Complete Agent Example - Codex Agentic Patterns](https://artvandelay.github.io/codex-agentic-patterns/learning-material/complete-agent-example/) - Implementation reference
- [Building Consistent Workflows with Codex CLI & Agents SDK](https://cookbook.openai.com/examples/codex/codex_mcp_agents_sdk/building_consistent_workflows_codex_cli_agents_sdk) - OpenAI cookbook example
- [The Agent Execution Loop: How to Build an AI Agent From Scratch](https://newsletter.victordibia.com/p/the-agent-execution-loop-how-to-build) - Foundational execution loop concepts
- [Agentic AI Workflows Design Patterns 2025](https://medium.com/codex/agentic-ai-workflows-design-patterns-examples-and-what-to-watch-in-2025-a3602b19b7e8) - Current patterns and direction
