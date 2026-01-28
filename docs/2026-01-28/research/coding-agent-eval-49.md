---
title: Comprehensive Coding Agent Evaluation - 49 Model and Agent Combinations
source: r/LocalLLaMA (lemon07r)
url: https://old.reddit.com/r/LocalLLaMA/comments/1qp4ftj/i_made_a_coding_eval_and_ran_it_against_49/
researched_at: 2026-01-28T00:00:00Z
fetch_method: concurrent-browser
---

# Comprehensive Coding Agent Evaluation - 49 Model and Agent Combinations

## Overview

A developer (lemon07r) built an open-source, agent-agnostic coding benchmark called **SanityHarness** and tested 49 distinct coding agent/model combinations against it. The resulting leaderboard (SanityBoard) provides reproducible, transparent evaluation data including run dates, agent versions, and full execution logs. This represents one of the most comprehensive independent coding agent benchmarks available, with strong emphasis on practical methodology and cost-performance analysis.

## Benchmark Methodology

### SanityHarness Design

**Test Coverage:**
- 26 tasks across 6 programming languages
- Single-file challenges to maintain evaluation speed and determinism
- Problems designed to measure actual understanding and agent capability rather than training data regurgitation
- Consistent, reproducible results even when retested

**Key Design Philosophy:**
- **Agent-agnostic approach**: Works with diverse agent architectures (unlike many existing benchmarks that favor specific platforms)
- **Determinism**: Single-file tasks produce repeatable results, enabling valid statistical comparison
- **Focus on problem-solving** rather than project setup/dependency management (multi-file tests planned as separate tier)

**Open Source & Transparent:**
- [GitHub: lemon07r/SanityHarness](https://github.com/lemon07r/SanityHarness) - Test implementation
- [GitHub: lemon07r/SanityBoard](https://github.com/lemon07r/SanityBoard) - Leaderboard frontend
- Full run data available on request for validation and model/agent improvement

### Evaluation Scope

The benchmark intentionally excluded 3 major coding agents due to monetization model concerns:
- **Warp** - Credit-based plan only
- **Letta Code** - No BYOK support
- **Codebuff** - Egregious pricing: 100 credits = $1.00, burned $7.50 to complete only 9 of 26 tasks (27% completion rate)

The author notes this exclusion highlights a critical market issue: platforms using credit systems refuse to support "Bring Your Own Key" (BYOK), suggesting they depend on inflated token metrics and poor value propositions to maintain margins.

## Results Architecture

### SanityBoard Leaderboard Features

**Data Transparency:**
- 49+ concurrent entries with growth planned
- Sort/filter controls for flexible analysis
- Per-run metadata: execution dates, agent versions, tool configurations
- Full execution logs available for reproducibility

**Metrics Tracked:**
- Success rate by task
- Execution efficiency (token usage patterns)
- Cost per successful run
- Agent/model combinations tested

**Advanced Testing Planned:**
- **MCP Tool Evaluation**: Testing which Model Context Protocol tools provide actual value vs. overhead, then testing optimal MCP combinations
- **Agent Skills Testing**: Evaluating pre-configured skill sets across projects
- **Model Variants**: Expanded testing (e.g., "Oh-My-Opencode" configurations vs. vanilla)

## Cost-Performance Analysis

### Pricing Context (per 26-task benchmark run)

**Efficient Performers:**
- AMP Smart Mode - $6.53 (noted as very efficient, fastest completion)
- AMP Rush Mode - $3.80
- Copilot CLI GPT 5.2 High - $0.86 (26 premium requests on pro plan)
- Kimi For Coding Plan (K2.5) - ~$0.30 (120-130 requests with $19/month 2k weekly limit)

**Platform-Specific Values (Factory/API Token Models):**
- Factory Gemini 3 Flash High - 1M tokens ($1)
- Factory GLM 4.7 High - 0.7M tokens
- Factory K2.5 - 0.8M tokens
- Factory Opus 4.5 High - 3M tokens

**Cautionary Example:**
Codebuff's pricing structure demonstrates predatory design: it advertises "30% less token usage than Claude Code" while maintaining per-token costs that result in $7.50 spent for 27% task completion. The markup appears intentional, forcing users toward credit purchases rather than API consumption.

## Practical Insights & Trade-offs

### Beyond Benchmark Scores

The author emphasizes critical limitations of pure benchmark evaluation:

**Model Selection Factors:**
- Leaderboard position doesn't correlate directly with user preference or real-world effectiveness
- Workflow integration and iterative interaction matter significantly (not captured in single-prompt evals)
- Many top-ranked models are not the author's preferred choices for actual development work
- Developer experience and agent affordances often outweigh raw benchmark performance

**Agentic Patterns:**
The author plans a comprehensive guide covering:
- Best open-weight models for coding
- Best open-source agents
- Optimal free-tier setups (Gemini CLI with Flash, Qwen Coder, Mistral Vibe mentioned as strong options)
- Value-optimized configurations for cost-conscious teams

### Noted Alternatives

**Free-Tier Options Not Captured in Leaderboard:**
- **Gemini CLI**: Recently improved with hidden "plan mode" in settings and useful prompt autocomplete
- **Qwen Coder**: Strong free tier performance
- **Mistral Vibe**: Reliable no-hassle option with Mistral Dev
- **Google Pro for Students**: 1 year free with student email

## Methodological Strengths

1. **Agent-Agnostic Design**: Solves a major problem in benchmarking—most evaluations are locked to specific platforms
2. **Transparency**: Run data, version info, and full logs available; author keeps all results for reproducibility
3. **Practical Metrics**: Includes cost analysis and real-world integration considerations
4. **Integrity Disclosures**: Acknowledges external credits/keys from providers (Droid usage, Minimax plan) while showing these don't inflate poor performers
5. **Reproducibility**: Single-file tests maintain consistency across reruns; author confirms thoroughness via complete reruns when issues found

## Architectural Considerations for Engineers

### Single-File vs. Multi-File Trade-off

Current design prioritizes:
- Speed of evaluation
- Determinism and statistical validity
- Core problem-solving assessment

Planned multi-file tier will address:
- Realistic project structures
- Cross-file dependency handling
- Build system interactions
- Linting/type-checking integration

This dual-tier approach allows fast iteration on core capability while planning comprehensive integration testing separately.

### MCP Tool Evaluation Strategy

The planned MCP testing represents sophisticated benchmarking:
1. **Isolated tool effect measurement**: Which tools actually move the needle?
2. **Combination analysis**: Tool synergies and negative interactions
3. **Overhead quantification**: Does web search, code indexing, or semantic retrieval justify latency costs?

This addresses a critical gap in agent evaluation—current benchmarks ignore external tool integration, which is essential for production systems.

## Implications

### For Platform Builders

1. **Pricing models matter**: Credit systems without BYOK are viewed as predatory; transparent API consumption (Factory tokens) are preferred
2. **Agent quality is differentiable**: 49 combinations show meaningful variance; arbitrage opportunities exist between cost and performance
3. **Iterative interaction is key**: Raw benchmark performance explains ~50% of user experience; workflow integration is the other half

### For LLM Teams

1. **Coding is a measurable capability** across models; no single provider dominates all metrics
2. **Efficiency matters**: AMP's strong performance despite lower cost suggests model quality and token efficiency can align
3. **Specialization pays**: Kimi K2.5 competitive pricing on dedicated coding tasks validates focused model design

### For Engineering Managers

1. **Avoid credit-only plans**: They signal poor economics; BYOK or public API models are more transparent
2. **Benchmark selection is critical**: Single-file evals miss integration issues; plan for multi-stage evaluation
3. **Cost-performance varies dramatically**: $0.30 to $7.50 for same task completion shows vendor discipline varies wildly

## Open Questions & Future Directions

- How much do MCP tools actually improve coding agent performance vs. latency/cost trade-offs?
- Do pre-configured skills generalize, or are they task-specific?
- How does model fine-tuning (e.g., "Oh-My-Opencode") compare to vanilla base models?
- What is the optimal team composition for multi-agent coding systems? (separate from single-agent benchmarks)

## Related Resources

- [SanityBoard Leaderboard](https://sanityboard.lr7.dev/) - Live results dashboard
- [GitHub: SanityHarness](https://github.com/lemon07r/SanityHarness) - Benchmark implementation
- [GitHub: SanityBoard](https://github.com/lemon07r/SanityBoard) - Leaderboard source
- [Author's Discord Community](https://discord.gg/rXNQXCTWDt) - Discussion and collaboration
- [Previous Guide Post](https://www.reddit.com/r/LocalLLaMA/comments/1o77ag4/a_guide_to_the_best_agentic_tools_and_the_best/) - Related deep-dive on agentic tools

---

**Note:** This evaluation represents Jan 28, 2026 snapshot. The author plans continuous expansion to 100+ combinations with MCP tool analysis, skill file testing, and multi-file benchmark tiers.
