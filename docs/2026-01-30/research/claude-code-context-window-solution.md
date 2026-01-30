# Claude Code Context Window Solution: Disciplined Project Management Workflow

| | |
|---|---|
| **Source** | Reddit r/ClaudeAI |
| **URL** | [old.reddit.com/r/ClaudeAI/comments/1qr23gq](https://old.reddit.com/r/ClaudeAI/comments/1qr23gq/claude_code_is_a_genius_but_it_has_the_memory_of/) |
| **Researched** | 2026-01-30 |

## Overview

An experienced Claude Code practitioner identified and solved the critical context management problem that causes AI-assisted coding to "forget" project architecture mid-session. Rather than a model limitation, the issue stems from poor context injection discipline. The solution leverages automated workflow enforcement through a tool called PropelKit that systematically manages context, enforces build phases, and verifies implementation—eliminating the "hallucination loop" that haunts large projects.

## Problem Statement

Claude Code exhibits what appears to be a memory problem: excellent code generation for ~10 minutes, then catastrophic hallucinations where it forgets database schemas, imports non-existent components, or violates established patterns. This isn't model degradation—it's **context mismanagement**. The model "gets lost in the weeds of small files" when context isn't deliberately structured.

## Proposed Solution Architecture

### Three-Tier Enforcement System

**1. The "Truth" File (REQUIREMENTS.md)**
- Single source of truth that Claude must read before every major edit
- Prevents speculation; forces systematic retrieval of existing schema/structure
- Acts as context anchor that overrides learned patterns from fragmented file history

**2. The "Phase" System**
- Decompose projects into strict sequential phases (e.g., Auth → Database → Payments)
- Claude explicitly forbidden from touching downstream phases until upstream phases verified
- Prevents architectural inconsistencies that emerge from out-of-order implementation
- Creates natural breakpoints for context reset when phase boundaries crossed

**3. The "Verifier" Agent**
- Separate verification loop running after each coding agent completion
- Validates that stated goal actually achieved vs. superficially "looks done"
- Catches hallucinations before they propagate downstream

### Implementation: PropelKit

SoftAd2420 bundled this workflow into a CLI tool that **automates context stuffing** so manual prompting isn't required every cycle. Key capabilities:

- **Automatic context injection**: Database schema, auth config, API structure force-fed before code generation
- **Phase locking enforcement**: System prevents Claude from violating phase dependencies
- **Automated testing**: 62+ edge-case tests run post-implementation (unicode, emoji handling, length constraints)
- **Execution audit trail**: Complete logged record of what Claude actually implemented and why

**Command interface example:**
```
/implement-phase 12
```
This single command triggers the full workflow: read requirements → inject context → implement → test → report.

## Key Architectural Decisions & Trade-offs

**Trade-off: Rigidity vs. Flexibility**
- Pro: Eliminates hallucinations, provides audit trail, catches bugs early
- Con: Requires upfront investment in project structure and phase boundaries
- Best for: Complex multi-phase projects (SaaS platforms with auth, database, payments)
- Risky for: Exploratory work, rapid prototyping where requirements undefined

**Trade-off: Overhead vs. Reliability**
- Regular workflow: Manual context management per conversation
- PropelKit: ~20% overhead for structured phase management
- Payoff: Near-complete elimination of cascading hallucinations that require rework

**Context Injection Strategy**
- Pre-scripted context hooks ensure consistency vs. model "drift" across tokens
- Differs from passive CLAUDE.md files that rely on model recall
- Mirrors human team practice: "read the spec before touching code"

## Community Feedback & Reception

**From comment thread:**
- Initial skepticism about differentiation from CLAUDE.md patterns
- Key insight acknowledged: **active context injection via automation >> passive context in docs**
- User feedback validates the phase-locking mechanism solves real pain point: out-of-order implementation creating cascading inconsistencies

**Adoption signal:**
- 63% upvote ratio indicates recognition of practical solution
- Tool already deployed in production SaaS project proving concept

## Implementation Considerations for Practitioners

**Prerequisites for effectiveness:**
1. Project must be decomposable into clear sequential phases
2. Requirements must be expressible in structured format
3. Must have test coverage for verification agent to leverage
4. Schema/configuration must be externalizable to injectable context

**Integration points:**
- Claude Code CLI wrapper approach (forces workflow participation)
- Razorpay-hardcoded in current release (Stripe variant in development)
- Works with existing codebase; no model changes required

**Risk factors:**
- Requires discipline: workflow only effective if followed consistently
- Overhead may be unjustified for small projects or POCs
- Current version India-focused; international users need Stripe adaptation

## Implications for Large-Scale AI-Assisted Development

This approach reveals a **critical architectural insight**: the model itself isn't the bottleneck for context management—**the prompting and context injection discipline is**. This suggests:

1. **Agentic workflows > single-turn interactions**: Structured multi-step processes with explicit verification gates outperform hope-based context recall

2. **Separation of concerns**: Distinct agents for generation, verification, and context management mirrors effective human team practices

3. **Measurable verification crucial**: The "Verifier Agent" isn't decorative—it's the circuit breaker that prevents hallucinations from compounding

4. **Auditability matters**: Execution logs enable post-mortem analysis and iterative refinement of phase boundaries

## Related Resources

- [PropelKit - Project Management Kit for Claude Code](https://propelkit.dev)
- REQUIREMENTS.md pattern (established practice, not novel but critical)
- Phase-based development methodology (domain-driven design alignment)
- Automated testing integration (standard practice, now integrated with AI workflow)
