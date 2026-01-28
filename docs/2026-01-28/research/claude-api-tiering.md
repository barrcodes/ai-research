---
title: Claude API Cost Optimization: 94.5% Reduction via File Tiering
source: Reddit r/ClaudeAI
url: https://old.reddit.com/r/ClaudeAI/comments/1qp9ve9/we_reduced_claude_api_costs_by_945_using_a_file/
researched_at: 2026-01-28T00:00:00Z
fetch_method: concurrent-browser
---

# Claude API Cost Optimization: 94.5% Reduction via File Tiering

## Overview

A production system (cortex-tms) achieved 94.5% reduction in Claude API costs by implementing a three-tier file classification system that selectively loads documentation into context windows. The approach reduces per-session costs from $0.11 to $0.01 for Sonnet 4.5 by eliminating unnecessary context overhead while maintaining AI reasoning quality.

## Key Points

- **Three-tier classification**: HOT (3,647 tokens, always loaded), WARM (10,419 tokens, on-demand), COLD (52,768 tokens, rarely needed)
- **Measurable impact**: Dogfooded across 66,834 total tokens; actual reduction from 66.8K to 3.6K tokens per session
- **Multi-model efficiency**: Cost reduction applies across Sonnet, Opus, and GPT-4 (10x reduction on GPT-4 queries: $1.20 → $0.11)
- **Secondary benefits**: Faster API responses (reduced context = reduced processing), improved output quality (no stale documentation), lower carbon footprint
- **Adoption signal**: 1,000+ NPM downloads (organic growth, no marketing spend), 93% upvote ratio on Reddit post

## Technical Details

### Architecture Pattern

Files tagged with tier markers (`<!-- @cortex-tms-tier HOT -->`) at declaration point. CLI tooling validates tiers and provides token visibility:

```bash
cortex status --tokens
```

Three distinct tiers address different consumption patterns:

1. **HOT tier**: Core implementation files, active task documentation, current context (3.6K tokens)
   - Always passed to Claude API
   - Focused scope prevents context dilution
   - Typically 5-10% of total documentation

2. **WARM tier**: Design patterns, glossaries, architecture decisions, recent documentation (10.4K tokens)
   - Loaded conditionally when developer references them
   - Balances completeness with token efficiency
   - Handles ~15% of standard inquiries without additional loading

3. **COLD tier**: Sprint retrospectives, ADRs, changelogs, archived specifications, historical context (52.8K tokens)
   - Minimal token budget impact when excluded (94.5% cost savings)
   - Explicitly referenced only when needed for context archaeology
   - Preserves institutional knowledge without API overhead

### Implementation Considerations

**Tier maintenance**: Files transition between tiers based on sprint cycles and feature activity. The system maintains automated token counting to validate tier assignments don't exceed target budgets.

**Context selection**: By default, only HOT tier loads. WARM and COLD tiers activate via explicit reference in prompts or automatic triggering when Claude detects missing context patterns.

**Backwards compatibility**: Token reduction doesn't degrade output quality—Claude produces better responses with focused context (eliminates contradiction resolution between old and current documentation).

## Implications for Practitioners

### Cost Optimization

For teams with large codebases (>500K tokens of documentation), tiering can reduce monthly API spend by 90%+. At current pricing ($3/MTok input for Sonnet 4.5), reducing context from 500K to 50K tokens saves $1.35 per session. With 10 sessions/day across a team, this compounds to $20K+/month savings.

### Architectural Decision

This pattern exposes a critical trade-off in AI-assisted development: **context breadth vs. reasoning quality**. Conventional wisdom suggests feeding complete context, but empirical results show focused context (homoiostatic documentation state) improves both latency and accuracy. Architects should consider context tiering as a first-class optimization target alongside code optimization.

### Operational Overhead

Tier management requires discipline—stale tagging creates false economy (paying API costs for irrelevant files). However, the cortex-tms tooling automates validation and token counting, reducing manual burden.

### Applicable Beyond Claude

The pattern generalizes to any tokenized context API (GPT-4, Claude Opus, future models). The principle—**hierarchical context loading based on access patterns**—mirrors database tiering strategies (hot/warm/cold data tiers in data warehouses).

## Related

- [cortex-tms on GitHub](https://github.com/cortex-tms/cortex-tms) - Open source MIT-licensed implementation
- [Full case study](https://cortex-tms.org/blog/cortex-dogfooding-case-study/) - Detailed methodology and token accounting
- NPM package: cortex-tms (1,000+ downloads)

## Technical Metrics

| Metric | Without Tiering | With Tiering | Reduction |
|--------|-----------------|--------------|-----------|
| Tokens/session | 66,834 | 3,647 | 94.5% |
| Cost (Sonnet 4.5) | $0.11 | $0.01 | 90.9% |
| Cost (GPT-4) | $1.20 | $0.11 | 90.8% |
| Context window usage | 100% | 5.5% | 94.5% |

**Note**: Costs computed on input tokens only; output tokens and multi-turn conversations would proportionally increase baseline costs, making tiering even more valuable for conversation-heavy workflows.