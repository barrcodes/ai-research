# Kimi K2.5 System Prompt and Tools Analysis

| | |
|---|---|
| **Source** | Reddit r/LocalLLaMA + GitHub dnnyngyen/kimi-k2.5-prompts-tools |
| **URL** | [old.reddit.com/r/LocalLLaMA/comments/1qoml1n/leaked_kimi_k25s_full_system_prompt_tools](https://old.reddit.com/r/LocalLLaMA/comments/1qoml1n/leaked_kimi_k25s_full_system_prompt_tools/) |
| **Researched** | 2026-01-28 |

## Overview

On January 27, 2026, the complete system prompt, tool schemas, and memory mechanisms for Moonshot AI's Kimi K2.5 model were extracted and publicly documented. Kimi K2.5 represents a Mixture-of-Experts (MoE) architecture with 1T parameters and a 256K context window. The extracted artifacts reveal sophisticated agentic patterns: structured tool orchestration, persistent memory management, context assembly protocols, and fine-grained guardrails.

## Key Architectural Insights

### Model Foundation
- **Architecture**: Mixture-of-Experts (MoE) with 1 trillion parameters
- **Context Window**: 256K tokens (enabling long-form reasoning and multi-turn workflows)
- **Multimodal**: Native visual understanding across inference-time tool use
- **Training**: ~15 trillion mixed visual and text tokens (continual pretraining on Kimi-K2-Base)

### System Prompt Structure
The system prompt is approximately 5,000 tokens and establishes three operational layers:

1. **Identity & Boundaries Layer**: Explicit declaration of capabilities, model name, and hard constraints
2. **Tool Specification Layer**: Complete function schemas with argument validation and error handling
3. **Context Assembly Layer**: Memory injection protocols, user profile contextualization, and external datasource integration

## Technical Details

### Tool Categories (8 Primary Families)

**1. Web Tools**
- `web_search`: Query-based internet retrieval (supports refinement/ranking)
- `web_open_url`: Direct fetch and DOM parsing (with HTML → plaintext conversion)
- Design: Kimi decides autonomously when to search vs. synthesize from training data

**2. Image Search Tools**
- `search_image_by_text`: Text-to-image retrieval (e.g., "images of Parisian cafes")
- `search_image_by_image`: Visual similarity search (reverse image lookup)
- Returns URLs + metadata; integrates with multimodal chain-of-thought

**3. Datasource Tools**
- `get_data_source_desc`: Enumerate available datasources (finance, arXiv, Wikipedia, etc.)
- `get_data_source`: Structured query interface (SQL-like filtering on datasource collections)
- Enables real-time market data, research paper retrieval, and domain-specific APIs

**4. Python Sandbox**
- `ipython`: Execute arbitrary Python code with output restrictions
- Kimi enforces token/output limits on generated code (prevents runaway computation)
- Returns stdout, stderr, and structured results (e.g., chart generation, data analysis)
- Context: Kimi's reasoning often precedes execution; code is a tool for validation, not ideation

**5. Long-Term Memory (Critical for Agentic Patterns)**
- `memory_space_edits`: CRUD operations on persistent memory blocks
- **Three operations**: ADD, REPLACE, DELETE across named memory spaces
- **Memory spaces**: User profiles, conversation summaries, extracted facts, preferences
- **Assembly**: Memory summaries prepended to new inference calls (injected as context)
- **Protocol**: Explicit "understand everything appended to this message" confirmation in memory edits

**6. File System Tools** (Inferred from context assembly)
- File read/write with path constraints
- Guardrails against path traversal
- Integration with long-term storage (not ephemeral)

**7. Context & Reasoning Tools**
- Token counting / context length monitoring
- Step-limit enforcement (to prevent infinite loops in agentic workflows)
- Explicit reasoning sections (`<kimi_internal_thoughts>` or similar markers)

**8. Citation & Attribution**
- Automatic link tagging when web/datasource tools are used
- Citation formatting: [text](url) markdown embedded in responses
- Supports user verification and trust signals

### Memory System Deep Dive

**Memory CRUD Protocol:**
```
memory_space_edits(
  operation: "ADD" | "REPLACE" | "DELETE",
  space_name: string,
  key: string,
  value: string (optional)
)
```

**Key Design Patterns:**
- Memory is **global across conversations** (persistent user context)
- Summaries are **automatically injected** before each inference (context assembly)
- Memory entries **versioned implicitly** (REPLACE overwrites; ADD fails if exists)
- Guardrails: Only model can edit; users cannot directly manipulate memory
- Use case: Remembering user preferences, conversation outcomes, extracted facts

**Context Assembly Flow:**
1. User initiates conversation
2. System retrieves user's memory summaries from named spaces
3. Memory blocks prepended to system prompt (e.g., "User prefers concise responses")
4. Inference proceeds with augmented context
5. Model can call `memory_space_edits` to update memories for future sessions

### Guardrails & Constraints

**Operational Boundaries:**
- Model **refuses** certain request types (direct jailbreak attempts, explicit harmful instructions)
- Model **acknowledges understanding** of all constraints before committing to tool use
- Memory edits require explicit step-by-step reasoning (transparency into agentic decisions)
- Step limits and token limits prevent runaway inference

**External Datasources:**
- Finance: Real-time market data, financial statements
- arXiv: Academic preprints with full-text search
- Wikipedia: Structured queries on encyclopedic knowledge
- Custom datasources: Integration hooks for enterprise APIs

**Chat Template & Reasoning Markers:**
- Tool calls use section markers: `<|tool_calls_section_begin|>` ... `<|tool_calls_section_end|>`
- Reasoning sections marked distinctly (separate from tool invocation)
- Prevents conflation of model reasoning with tool output

## Implications for Practitioners

### 1. Agentic System Design
This architecture reveals a **production-grade pattern** for LLM-based agents:
- Separate **tool call invocation** from **reasoning/planning**
- Use **persistent memory** to maintain behavioral consistency across sessions
- Implement **explicit context assembly** rather than relying on in-context learning alone
- Enforce **step/token limits** to prevent pathological loops

**Actionable insight**: When building agents, treat memory as a first-class abstraction. Kimi's memory_space_edits is not a nice-to-have; it's essential for multi-turn coherence.

### 2. Tool Orchestration Trade-offs
- **Web search + datasource**: Kimi exposes both retrieval paths; system prompt guides selection
- **Real-time vs. training-data**: Model decides when internet lookup is necessary vs. when training knowledge suffices
- **Tool latency**: Kimi's 256K context can absorb multiple tool calls + results without fragmentation

**Actionable insight**: Design tool sets conservatively. Too many tools increase hallucination risk; too few reduce capability. Kimi uses ~8 categories—a reasonable upper bound.

### 3. Reasoning Transparency
The extracted prompt reveals **explicit reasoning sections** distinct from tool calls:
- Kimi separates internal thinking from user-facing responses
- Tool invocations are **deliberate acts**, not side effects of generation
- This mirrors recent research on chain-of-thought and reasoning as distinct phases

**Actionable insight**: When implementing reasoning, use section markers or explicit separation. This enables debugging, auditing, and reduces spurious tool calls.

### 4. Memory Persistence as Behavioral Anchor
Unlike stateless APIs, Kimi's memory system enables:
- **Personality consistency**: User preferences propagate across sessions
- **Learning from feedback**: Conversation outcomes update memory for future interactions
- **Multi-hop reasoning**: Complex tasks span multiple inference calls; memory bridges gaps

**Actionable insight**: If scaling local agents, implement analogous memory. It dramatically improves UX for long-lived assistants.

### 5. Context Window Economics
A 256K context window enables:
- **Full conversation history** without aggressive summarization
- **Multi-document reasoning** (e.g., compare multiple papers)
- **Large codebase context** (e.g., whole repository for refactoring tasks)

**Actionable insight**: Larger context windows shift the engineering burden from summarization logic to simple concatenation. For agents with many tools, invest in context.

### 6. Guardrails & Transparency
The system prompt includes **explicit refusal criteria** and **reasoning confirmations**:
- Before executing sensitive operations, Kimi confirms understanding
- Refusals are **reasoned**, not arbitrary
- This mitigates adversarial jailbreaking while preserving legitimate use

**Actionable insight**: Guardrails should be explicit in the prompt, not hidden in post-hoc filters. Transparency reduces user frustration and improves safety.

## Verification & Provenance

- **Original extraction**: Verified via inference-time introspection (guided prompting on Kimi K2.5)
- **No binary reverse engineering** or unauthorized access
- **Independent verification**: Multiple sources (linux.do, reddit r/ClaudeAIJailbreak) confirmed same artifacts
- **Methodology**: Model self-reporting when prompted to explain its own constraints

**Limitations:**
- This snapshot reflects Kimi K2.5 at release (Jan 2026); future versions may diverge
- System prompt extraction relies on model cooperation; adversarial prompts may not reveal full logic
- Tool availability may differ between API endpoints and local deployments

## Related

- [GitHub: dnnyngyen/kimi-k2.5-prompts-tools](https://github.com/dnnyngyen/kimi-k2.5-prompts-tools) - Complete extracted artifacts (system prompt, tools.json, context assembly format, ANALYSIS.md)
- [Hacker News discussion](https://news.ycombinator.com/item?id=46775961) - Technical community reaction and additional analysis
- [Kimi K2.5 Technical Report](https://www.kimi.com/blog/kimi-k2-5.html) - Official Moonshot AI announcement
- [Hugging Face Model Card](https://huggingface.co/moonshotai/Kimi-K2.5) - Open-source model weights and inference details
- [NVIDIA NIM Integration](https://build.nvidia.com/moonshotai/kimi-k2.5/modelcard) - Production deployment guidance

---

**Keywords**: Kimi K2.5, system prompt, agentic architecture, tool calling, memory persistence, context assembly, MoE, 1T parameters, 256K context, prompt engineering, LLM agents
