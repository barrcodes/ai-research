# AGENTS.md Outperforms Skills in Agent Evaluations

| | |
|---|---|
| **Source** | Vercel |
| **URL** | [vercel.com/blog/agents-md-outperforms-skills-in-our-agent-evals](https://vercel.com/blog/agents-md-outperforms-skills-in-our-agent-evals) |
| **Researched** | 2026-01-30 |

## Overview
Vercel's research demonstrates that embedding framework documentation directly in system prompts (AGENTS.md approach) achieves 100% task completion rates, dramatically outperforming active retrieval systems (skills). The key finding: passive context availability eliminates the decision overhead that undermines agent reasoning, even when documentation is nominally available.

## Key Points
- **AGENTS.md achieves 100% pass rate** vs 53% baseline and 79% with optimized skills instruction
- **Active retrieval fails 56% of the time** - agents don't recognize when to invoke skills despite having them available
- **8KB compressed documentation index** in system prompt is more reliable than asynchronous skill invocation
- **Static context beats dynamic retrieval** for general framework knowledge, eliminating sequencing confusion and loading delays
- **Methodology strength:** Tested against APIs absent from training data (Next.js 16 `'use cache'`, `connection()`) to avoid model hallucination

## Technical Details

| Approach | Pass Rate | Key Limitation |
|----------|-----------|-----------------|
| Baseline (no docs) | 53% | No framework context |
| Skills (default) | 53% | Agents don't invoke retrieval |
| Skills + explicit instructions | 79% | Fails on edge cases |
| AGENTS.md doc index | 100% | Fixed-size context overhead |

The skills approach required agents to make a meta-decision: "Do I need documentation now?" This decision point failed systematically. The AGENTS.md approach eliminated this requirement by embedding a compressed 8KB index directly in the system prompt, making documentation persistently available across all interaction turns without asynchronous delays or retrieval sequencing confusion.

## Implications
- **For agent architecture:** Passive context availability outweighs sophisticated retrieval for general framework knowledge—the decision overhead costs more than context size overhead
- **Skills still have value:** Skills remain appropriate for user-initiated workflows, domain-specific procedures, or when index size becomes prohibitive
- **Context budget trade-off:** Teams should evaluate whether the 8KB hit on prompt tokens (vs retrieving only when needed) is worth eliminating agent retrieval decision logic
- **Broader pattern:** This challenges the retrieval-augmented generation (RAG) assumption that agents should *decide* what they need; sometimes forcing availability is more reliable

## Related
- [Vercel AI SDK documentation](https://sdk.vercel.ai) - Implementation patterns for agentic systems
- [Chain-of-Thought research](https://arxiv.org/abs/2201.11903) - Related work on reasoning reliability in LLMs
