# Unrolling the Codex Agent Loop

| | |
|---|---|
| **Source** | OpenAI Engineering |
| **URL** | [openai.com/index/unrolling-the-codex-agent-loop/](https://openai.com/index/unrolling-the-codex-agent-loop/) |
| **Researched** | 2026-01-31 |

## Overview

OpenAI's Codex CLI implements a production agent loop architecture that orchestrates interactions between users, LLMs, and tools. This post dissects the core orchestration patterns, prompt construction strategies, and critical performance optimizations (prompt caching, context window management) that enable reliable software agent execution at scale.

## Key Points

- **Stateless Request Design**: Codex prioritizes stateless HTTP requests to the Responses API over the `previous_response_id` parameter, enabling Zero Data Retention (ZDR) support and simplified provider implementations. This architectural choice trades potential efficiency for security/compliance guarantees.

- **Prompt Prefix Reuse for Cache Hits**: The agent loop maintains prompt prefixes across turns—every new inference includes all prior messages as exact prefix matches. This enables prompt caching to reduce model sampling from O(n²) to O(n), making sampling linear rather than quadratic with conversation length.

- **Structured Input Composition**: Initial prompts are built from layered, ordered components: system instructions, developer instructions, user/project-level instructions (aggregated from AGENTS.md files in git repo), sandbox/permission directives, and environment context. This hierarchy allows fine-grained control over model behavior without breaking cache.

- **Tool Definition Consistency**: MCP server tools must enumerate consistently (deterministic order) to avoid cache misses. Tool list changes via `notifications/tools/list_changed` mid-conversation can cause expensive cache invalidation—a critical insight for tool-based agent architectures.

- **Configuration Changes as Append-Only Events**: Mid-conversation changes to sandbox mode, approval policy, or working directory are appended as new messages rather than mutating earlier items. This preserves cache hits by keeping the original prompt prefix intact.

- **Automatic Context Window Compaction**: When token count exceeds thresholds, Codex invokes the Responses API's `/responses/compact` endpoint, which returns opaque `encrypted_content` items that preserve the model's latent understanding while freeing context window space. This prevents conversations from exhausting available tokens.

## Technical Details

### Agent Loop Mechanics

The Codex agent loop follows the standard LLM agent pattern:
1. User input → Prompt composition
2. Model inference via Responses API (HTTP POST with JSON payload)
3. Parse response: either assistant message (turn end) or function_call request
4. Execute tool, append result to prompt
5. Re-query model with expanded context
6. Repeat steps 3-5 until assistant message received

### Responses API Integration

- **Configurable Endpoints**: Codex supports multiple backends—OpenAI's hosted API, local ollama/LM Studio with gpt-oss, ChatGPT backend-api, Azure—all implementing the same Responses API contract.

- **Message Roles and Priority**: Prompts use role hierarchy (system > developer > user > assistant) to control weight. The API server structures final prompt regardless of client order; client controls tools, instructions, and input content.

- **Streaming Output**: Server-Sent Events (SSE) stream response deltas. Events like `response.output_text.delta` support streaming UI; `response.output_item.added` events are transformed into objects appended to the input array for subsequent calls.

### Performance Optimization: Prompt Caching

- **Cache Invalidation Hazards**: Changing tools mid-conversation, switching models, or modifying sandbox config causes cache misses. The Codex team discovered a bug where MCP tools weren't enumerated in consistent order, silently breaking cache hits.

- **Append-Only Configuration**: Rather than mutating early messages, configuration changes are appended as new messages with matching format to the original directive. This preserves the cache-hit prefix.

### Context Window Management

Codex uses two strategies:
1. **Token Threshold Monitoring**: Tracks token count; when exceeding `auto_compact_limit`, triggers compaction.
2. **Opaque Compaction State**: The `/responses/compact` endpoint returns a `type=compaction` item with encrypted_content. This preserves model understanding without exposing conversation history, critical for ZDR compliance (OpenAI holds decryption keys but not data).

## Implications

**For Agent Architects:**

1. **Stateless Design Trades**: Avoiding `previous_response_id` sacrifices efficiency for ZDR support. Evaluate this trade-off: linear JSON growth (every request includes prior context) vs. compliance/simpler server implementations.

2. **Cache-Aware Prompt Engineering**: Prompt caching transforms agent economics. Static instructions must appear early; dynamic content last. Tool definitions and configuration changes are expensive. This is a first-order concern in agent system design, not an afterthought.

3. **Tool Enumeration as Contract**: MCP or custom tools must guarantee deterministic ordering. Non-deterministic tool lists are a subtle performance killer—clients won't detect cache misses locally.

4. **Conversation Compaction as Architecture Requirement**: Long conversations require compaction to stay within model context windows. The API-level support for opaque `encrypted_content` preservation is novel—allows the model's reasoning to survive compaction without exposing history.

5. **Multi-Turn State Management Complexity**: Each turn appends assistant message + tool results to input. Payload size grows with every iteration. The burden falls on the client (Codex) to manage this growth and trigger compaction—not automatic at API level.

**For LLM Service Providers:**

- Responses API design enables clients to optimize locally for cache hits. Stateless requests mean providers don't hold state, but clients must carefully manage prompt structure. The encrypted_content approach for ZDR is architecturally elegant—model understanding survives without data persistence.

## Sources

- [OpenAI Codex CLI Repository](https://github.com/openai/codex) - Open source implementation with detailed PRs on prompt caching and ZDR support
- [Responses API Documentation](https://platform.openai.com/docs/api-reference/responses) - API contract details
- [Prompt Caching Guide](https://platform.openai.com/docs/guides/prompt-caching) - Performance optimization patterns
- [Conversation State & Compaction](https://platform.openai.com/docs/guides/conversation-state) - Context window management strategies
