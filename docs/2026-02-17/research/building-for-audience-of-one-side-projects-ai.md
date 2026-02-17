# Building for an audience of one: starting and finishing side projects with AI

| | |
|---|---|
| **Source** | codemade.net |
| **URL** | [codemade.net/blog/building-for-one](https://codemade.net/blog/building-for-one/) |
| **Researched** | 2026-02-17 |

## Overview

A practical case study on using LLM agents (Claude, Gemini) to bootstrap and complete side projects end-to-end. The author built FastTab—an X11/Plasma task switcher in Zig with OpenGL rendering—demonstrating how specification-first design, container isolation, and strategic human oversight let AI handle ~80% of the work while humans provide domain expertise for the remaining 20%.

## Key Points

- **Start with conversation, not code**: Explore problem space with the LLM before writing specifications. Use pseudocode and Mermaid diagrams instead of full implementations—keeps token costs down and maintains focus on architecture.

- **AI delivers 80%, humans deliver 20%**: The LLM excels at generating functional code quickly, but success requires domain knowledge (e.g., knowing when to request SIMD optimization), refactoring monolithic output into modular structures, and architectural review before iteration cycles.

- **Containerize + Git staging = safety**: Use sandboxed environments (Docker) to prevent accidental system damage while granting filesystem access to agents. Leverage git staging area to review diffs incrementally—enables rollback without risk.

- **Token consumption is a real constraint**: Complex languages like Zig burn tokens faster than Python/JavaScript. Author switched between Claude and Gemini when hitting rate limits, suggesting multi-model fallback strategies matter for sustainable iteration.

## Technical Details

**Project**: FastTab—daemon-based X11/Plasma task switcher (language: Zig, rendering: OpenGL)

**Workflow**:
1. Conversation to identify requirements and problem space
2. Request detailed specification with architecture milestones (not raw code)
3. AI generates implementation in phases
4. Human: review diffs, refactor for modularity, identify architectural gaps
5. Iterate with targeted follow-ups informed by domain knowledge

**Tools Used**: Claude (primary), Gemini 3 (fallback), oh-my-opencode (abandoned—token overhead), claude-code CLI for agent orchestration

**Safety Pattern**: Customized Docker wrapper (`contai`) creates isolation boundary, preventing filesystem destruction while maintaining git history as revert mechanism.

## Implications

**For practitioners**: AI-assisted development changes the completion curve for side projects. Motivation typically wanes before technical completion; AI compression of the middle 60% of work lets you ship before abandonment. However, this requires competence in your problem domain—the tool amplifies domain expertise, not replaces it.

**Trade-offs**:
- Token limits force model switching and artificial scope boundaries (not a code quality problem, an economics problem)
- Monolithic AI-generated code requires human refactoring investment upfront
- Specification clarity directly impacts iteration efficiency—vague requirements burn tokens

**When to apply this pattern**: Side projects, proof-of-concepts, internal tooling. For production systems, the 20% human portion grows significantly; this pattern assumes acceptable technical debt for time-to-value.

**Architectural decision**: Start with spec-first, conversation-driven design before any code exists. This prevents gold-plating and token waste that pure prompt-engineering approaches incur.

## Sources

- [codemade.net/blog/building-for-one](https://codemade.net/blog/building-for-one/) - Full case study on FastTab development with Claude agents