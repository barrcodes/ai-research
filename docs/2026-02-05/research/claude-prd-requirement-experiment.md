# Forcing Claude to Require a PRD Before Writing Code

| | |
|---|---|
| **Source** | Reddit r/ClaudeAI |
| **URL** | [old.reddit.com/r/ClaudeAI/comments/1qwgpfl/](https://old.reddit.com/r/ClaudeAI/comments/1qwgpfl/i_forced_claude_to_reject_my_code_until_i_wrote_a/) |
| **Researched** | 2026-02-05 |

## Overview

A developer implemented a self-imposed constraint: Claude must reject code requests until a Product Requirements Document (PRD) exists. After one month of this practice, the pattern fundamentally changed how they prompt and build with Claude Code. The experiment revealed a direct relationship between specification clarity and output quality—eliminating the "80% right, 100% broken" loop that plagues ad-hoc prompting.

## Key Points

- **The Problem**: Without specs, Claude builds exactly what's asked for and skips everything unstated (password reset, rate limiting, session expiry). This creates 2-3 hour re-prompt-debug cycles rather than one-shot delivery.

- **The Constraint**: No code until there's a PRD answering: what does this do? What are inputs/outputs? What are edge cases? What does "done" look like?

- **The Shift**: By day 3, the workflow changed. After a month: zero features rewritten from scratch (was ~2/week), dramatically shorter time from request to working code, and 23 PRDs written (averaging 8 minutes each).

- **Multi-Persona Review**: Adding AI personas (security-focused, QA) to review specs before build caught architectural gaps the developer would've shipped. This became the critical leverage point.

- **Concrete Example - License Activation Flow**:
  - Without spec: Only activation endpoint. Missing machine binding, offline grace period, deactivation.
  - With spec + personas: QA asked "What happens on machine switch?" Security asked "How verify permissions?" Both made it into the spec. Claude built it all correctly.

## Technical Details

The key architectural insight is that Claude operates best with **explicit decision points resolved upfront**, not discovered during implementation. A PRD forces resolution of:

- Data flow and state management
- Error handling and edge cases
- Security boundaries and validation rules
- Integration points and dependencies

This shifts the interaction model from "iterative refinement through debugging" to "specification-first delivery." The developer noted that forcing the spec-first habit changed how they think about prompting—moving from vague natural language ("build login") to precise requirements documents.

**Process Changes**:
- Feature requests → PRD → Claude build → one-shot delivery
- Replaced: Spec → code → missing piece → re-prompt → debug cycle
- PRD review time (8 minutes average) is recouped 2-3x in eliminated re-prompt cycles

## Implications

For teams using Claude Code or similar agentic systems:

1. **Specification is not overhead—it's acceleration**. The mental model should be "spec-first reduces total time-to-delivery" rather than "specs slow us down."

2. **AI agent compliance depends on explicit context**. Claude follows trigger-action rules better than vague descriptions. A PRD is a trigger-action document; it compels specificity that vague prompts don't.

3. **Personas as gatekeepers are surprisingly effective**. Using AI to review specifications before passing them to implementation agents caught issues that would've required post-build fixes. This is a pattern worth systematizing.

4. **The 80/100 problem is real**. Code that's 80% right but 100% broken (missing critical pieces) is worse than partial code. Specs eliminate this by making completeness explicit.

5. **This scales to teams**. If one person's PRD-first discipline is this effective, teams standardizing on PRDs before agent tasks could unlock similar multipliers across the board.

## Sources

- [I forced Claude to reject my code until I wrote a PRD — what happened after a month](https://old.reddit.com/r/ClaudeAI/comments/1qwgpfl/i_forced_claude_to_reject_my_code_until_i_wrote_a/) - Original Reddit post by Savings-Abalone1464, discussing month-long experiment with PRD-first development
