# AI-Powered Kanban Board for Autonomous Development Pipelines

| | |
|---|---|
| **Source** | Reddit r/ClaudeAI |
| **URL** | [old.reddit.com/r/ClaudeAI/comments/1rdgfrq/](https://old.reddit.com/r/ClaudeAI/comments/1rdgfrq/i_built_a_kanban_board_where_claudes_ai_agents/) |
| **Researched** | 2026-02-24 |

## Overview

A developer (cyanluna) built a specialized kanban board system where Claude agents autonomously handle the entire software development pipeline—from requirements planning through implementation, code review, testing, and auto-commit. The system addresses a critical pain point in AI-assisted development: context loss and work state management across sessions. Rather than mimicking tools like Jira for humans, this is purpose-built for agent-to-agent handoffs with explicit role boundaries and structured output.

## Key Points

- **Pipeline architecture**: 7-column workflow with specialized agents at each stage (Planner → Critic → Builder → Inspector → Ranger)
- **Agent composition**: Uses Opus for planning/implementation (complex reasoning) and Sonnet for review/validation (fast verification), with explicit model routing per task
- **Context-first design**: Every card maintains a complete audit trail—requirements, plan, review comments, implementation notes, test results, commit hash—solving the "lost context across sessions" problem
- **Structured handoffs**: Each agent uses fixed nicknames and timestamps in output, creating an immutable work record queryable later
- **Flexible pipeline levels**: L1 (quick fixes), L2 (standard features), L3 (full spec→review loops), allowing different task complexity to skip unnecessary steps
- **Local-first infrastructure**: SQLite per-project database, no SaaS dependency, Vite-based web UI (localhost:5173)
- **Parallel execution**: Can run 3+ cards simultaneously, though practically 1-2 at a time remains manageable

## Technical Details

### Agent Roles & Model Selection

| Agent | Model | Function |
|-------|-------|----------|
| **Planner** | Opus | Converts requirements to structured implementation plan |
| **Critic** | Sonnet | Reviews plan for clarity, testability, reversibility; approves or rejects |
| **Builder** | Opus | Implements code following approved plan |
| **Shield** | Sonnet | Writes TDD tests alongside implementation |
| **Inspector** | Sonnet | Performs code review; flags architecture violations |
| **Ranger** | Sonnet | Runs lint, build, e2e tests; auto-commits with `[kanban #ID]` tag |

**Model routing rationale**: Opus handles the "hard thinking" (planning, multi-step implementation), while Sonnet validates/reviews/executes deterministic tasks (linting, testing)—an explicit cost optimization within a fixed budget.

### Storage & Persistence

- **Per-project SQLite database**: `~/.claude/kanban-dbs/{project}.db` (one file per codebase)
- **Cross-PC sync**: Symlink to OneDrive for unified access across macOS/WSL with different physical paths
- **Web board**: Vite + TypeScript, auto-refreshes every 10s, drag-and-drop between columns enforces valid state transitions
- **Skill integration**: Installed as Claude Code skill (`/kanban-init`, `/kanban run <ID>`)

### Critical Feature: Context Management

The designer's fundamental insight: "Claude is doing next-token prediction. The better the context you give it, the better the output."

Each card record includes:
- **Requirements**: Original user description + screenshots
- **Plan**: Planner's structured breakdown
- **Plan review comments**: Critic's feedback with approval/rejection rationale
- **Implementation notes**: Builder's decisions and code summary
- **Test results**: Ranger's CI/CD output
- **Commit hash**: Final git reference

This eliminates the "re-onboarding cost" when resuming after a week away—pull up the relevant card column and everything is there.

### Pipeline Stages with Validation

1. **Plan→Review Plan loop**: Critic provides structured rubric feedback (clarity/testability/reversibility scores, recently added) instead of binary approve/reject
2. **Impl→Review Impl**: Inspector checks against plan; defects loop back to Builder
3. **Test→Done**: Ranger's passing CI automatically commits; failing tests raise flags
4. **Circuit breaker**: Automatic abort after 3+ review loops to prevent infinite feedback cycles

## Architectural Patterns & Trade-offs

### Separation of Concerns
Unlike monolithic "do everything" agent prompts, this distributes responsibility: planner stays in requirements, critic stays in validation, builder executes. Reduces hallucination by constraining each agent's scope.

### The Context vs. Cost Trade-off
- **Pro**: Complete audit trail prevents lost work and re-derivation
- **Con**: Every handoff is an API call with full card context (tokens consumed per stage)
- **Mitigation**: SQLite-backed architecture allows selective context retrieval; web UI filters by project/status

### Synchronous Validation vs. Speed
- The Planner→Critic loop is blocking (no auto-approval unless `--auto` flag)
- Design assumes human-in-the-loop at review gates, but fully automatable
- Community feedback noted "rubber-stamp" problem: Critic tends to approve plans wholesale—structured rubrics (newly added) force tighter feedback

### No Multi-Agent Coordination Protocol
Unlike systems with explicit consensus or voting (e.g., debates between competing models), this uses linear pipeline. Tradeoff: simpler execution, but Inspector/Ranger operate downstream only. Suggestion from community: add "decision log" field per card to capture *why* architectural choices were made, avoiding re-derivation on future tasks.

## Practical Implications for Teams

### When This Solves a Real Problem
1. **Vibe-coding fatigue**: If you're losing track of sessions and re-explaining context, this eliminates that
2. **Context window anxiety**: Cards become a queryable "project memory" that doesn't consume tokens every call
3. **Audit trail requirements**: Every decision is timestamped and attributed to a specific agent/model
4. **Multi-project context switching**: One unified board filters by project; agents pick up exactly where they left off

### Implementation Challenges
1. **Prompt engineering at scale**: Each agent gets a template; tuning Planner, Critic, Builder prompts for your specific stack is ongoing
2. **Model cost**: Planner+Builder use Opus (expensive); cost per task depends on code complexity and number of review loops
3. **Coordination overhead**: Parallel execution (3+ cards) requires monitoring; sequential (1-2 cards) is simpler to reason about
4. **Portable, but not plug-and-play**: System is Claude-specific (Claude Code skills), though "concepts should be portable to other setups"—but MCP tooling, Sonnet/Opus model selection, skill syntax are Anthropic-specific

### Comparison to Existing Approaches
- **GitHub Issues + MCP**: Simpler, native, but lacks specialized agent roles and doesn't enforce pipeline semantics
- **Linear + MCP**: Powerful for team coordination, but not designed for agent-specific state machines
- **Jira + Claude integration**: Works for team tracking; this trades richness for agent-first UX
- **Markdown files**: What the author used before; gets unwieldy at scale, lost searchability

### Community Response Themes
- **Enthusiasm for context management**: BP041 validated "context management is the unsolved problem in vibe coding"
- **Suggestion to add decision logs**: Avoid re-derivation when returning to tasks
- **Skepticism on novelty**: Multiple commenters noted similar systems exist (oh-my-opencode, openclaw, others); consensus: everyone's building this, but customization to your stack matters more than the tool
- **Questions on scalability**: DarkSkyKnight flagged that multi-layer decomposition (high-level plan → tactical plans → implementation) still requires human mental model; Sonnet as critic is weak for complex architectural decisions
- **SaaS-free appeal**: Local SQLite + no accounts resonates vs. cloud-based kanban tools

## Sources
- [Reddit post by cyanluna - Kanban board for Claude agents](https://old.reddit.com/r/ClaudeAI/comments/1rdgfrq/i_built_a_kanban_board_where_claudes_ai_agents/) - Full post with 23 comments, posted 2026-02-24
- [GitHub repository - cyanluna.skills](https://github.com/cyanluna-git/cyanluna.skills) - Open source MIT implementation with TypeScript web UI, 47 stars, includes pipeline templates and schema
