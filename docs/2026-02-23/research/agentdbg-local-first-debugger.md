# AgentDbg - Local-First Debugger for AI Agents

| | |
|---|---|
| **Source** | GitHub |
| **URL** | [github.com/AgentDbg/AgentDbg](https://github.com/AgentDbg/AgentDbg) |
| **Researched** | 2026-02-23 |

## Overview

AgentDbg is a development-time debugger that captures structured execution traces of AI agents locally, recording LLM calls, tool invocations, errors, and state changes. It solves a real problem: agent failures often hide in opaque logs with no visibility into why an agent looped indefinitely, miscalled a tool, or drifted from expected behavior. The tool targets developers building agentic systems who need to understand what actually happened during execution without shipping to production.

## Key Points

- **Minimal instrumentation overhead**: Decorator-based API (`@trace`) wraps agent functions; manual recording functions (`record_llm_call`, `record_tool_call`) integrate with existing code with minimal refactoring
- **Framework-agnostic design**: Works with any Python agent code, not tied to specific frameworks (though LangChain/LangGraph adapters exist)
- **Privacy-first local storage**: All traces stored as JSON in `~/.agentdbg/runs/`; sensitive data (API keys, tokens, passwords) redacted before writing to disk
- **Automatic loop detection**: Identifies repetitive execution patterns with evidence, flagging a common failure mode
- **Timeline visualization**: Web UI at `http://127.0.0.1:8712` shows chronological events with expandable details including prompts, responses, token usage, and tool arguments

## Technical Details

**Storage Model**: Plain JSON files (metadata in `run.json`, events in `events.jsonl`) enable local inspection and git-friendly versioning.

**CLI Interface**:
```bash
agentdbg list          # Last 20 runs
agentdbg view          # Open timeline UI
agentdbg export <RUN_ID> --out run.json
```

**Core API** requires three imports:
```python
from agentdbg import trace, record_llm_call, record_tool_call

@trace
def run_agent():
    record_tool_call(name="search", args={...}, result={...})
    record_llm_call(model="gpt-4", prompt="...", response="...", usage={...})
```

**Scope**: Explicitly positioned as a development tool, not production observability. No hosted backend, no deterministic replay (v0.2+ planned), no dashboards or alerting.

## Implications

This tool fills a gap between local debugging and production monitoring. For teams building agents:

- **When to use**: Early development, debugging agent loops, investigating tool schema mismatches, and reproducing prompt regressions. The local-first model means zero setup friction and full privacy.
- **Trade-off**: Solves visibility problems but lacks production-scale observability (sampling, aggregation, alerting). You'll still need a separate observability platform for production monitoring.
- **Integration strategy**: Can be left in code long-term (low overhead) or stripped in production, since it only writes to local disk and doesn't ship data.
- **Architectural decision**: Framework-agnostic design is both strength (works anywhere) and potential friction (no built-in integrations for every framework—you manually record LLM/tool calls in custom code).

For teams at scale, AgentDbg complements rather than replaces production tracing systems. It's most valuable in the iteration loop where understanding *why* an agent failed matters more than *how often* it fails.

## Sources

- [AgentDbg Repository](https://github.com/AgentDbg/AgentDbg) - Local-first AI agent debugger with structured execution tracing
