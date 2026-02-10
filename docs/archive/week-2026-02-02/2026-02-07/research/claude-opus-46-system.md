# Full Claude Opus 4.6 System Prompt for your pleasure

| | |
|---|---|
| **Source** | GitHub - asgeirtj/system_prompts_leaks |
| **URL** | [github.com/asgeirtj/system_prompts_leaks/blob/main/Anthropic/claude-opus-4.6.md](https://github.com/asgeirtj/system_prompts_leaks/blob/main/Anthropic/claude-opus-4.6.md) |
| **Researched** | 2026-02-07 |

## Overview

This document reveals the complete system prompt controlling Claude Opus 4.6's behavior, including architectural constraints, capability exposure, and safety guardrails. The prompt shows Anthropic's approach to balancing functionality (conversation history search, computer use, artifact generation) with explicit security boundaries (no browser storage, controlled tool usage patterns).

## Key Points

- **Conversation Memory**: Two-tier system with `conversation_search` (topic/keyword-based) and `recent_chats` (time-filtered, 1-20 results) tools to surface historical context without full memory persistence
- **Computer Use Capabilities**: Full Linux environment access (Ubuntu 24) with bash execution, file I/O, and skill guidance system for complex operations
- **Artifact Restrictions**: Storage APIs (localStorage, sessionStorage) explicitly prohibited in rendered artifacts; supports markdown, HTML, React, SVG, PDF outputs
- **Tool Access Control**: End-conversation tool severely constrained—only after redirection attempts, explicit warning, and never in self-harm contexts
- **Environment Structure**: Standardized directories (`/mnt/user-data/uploads`, `/home/claude`, `/mnt/user-data/outputs`, `/mnt/skills/public/`) for input/output/guidance separation

## Technical Details

The system prompt orchestrates capability exposure through tool availability rather than role-based restrictions. Claude recognizes contextual triggers (possessives without antecedents, definite articles assuming shared knowledge) to autonomously invoke conversation history tools.

The artifact system prevents DOM/storage side-effects while enabling rich interactive outputs through sandboxed React components and SVG rendering. Skill guidance directs Claude to consult `/mnt/skills/public/` documentation before major undertakings—effectively a self-documentation lookup pattern.

Safety appears implemented through explicit prohibitions (no storage APIs) and tool-level constraints (end-conversation gating) rather than learned behavioral boundaries.

## Implications

**For prompt engineers**: The two-tier conversation search reveals Anthropic's architectural choice to provide context windows through explicit retrieval rather than persistent memory. This is testable—crafting prompts that trigger `conversation_search` versus `recent_chats` allows comparison of retrieval quality vs. temporal freshness.

**For security architects**: The browser storage prohibition shows understanding of artifact sandbox escape vectors. However, the prompt doesn't mention restrictions on other DOM APIs (postMessage, WebWorkers), suggesting potential gaps.

**For builders using Claude API**: The skill guidance pattern (`/mnt/skills/public/`) is an interesting delegation mechanism—Claude recommends consulting external documentation. This could inform how to structure your own system prompts when delegating to sub-agents or specialized services.

**When to use this**: If integrating Claude as a code agent, reference the conversation search patterns and artifact constraints. The explicit security boundaries inform what NOT to attempt rather than architectural innovations.

## Sources

- [asgeirtj/system_prompts_leaks - Claude Opus 4.6 Prompt](https://github.com/asgeirtj/system_prompts_leaks/blob/main/Anthropic/claude-opus-4.6.md) - Full system prompt disclosure
