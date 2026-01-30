# We Studied 150 Developers Using AI (Here's What's Changed)

| | |
|---|---|
| **Source** | Anthropic + METR Research |
| **URL** | [anthropic.com/research/how-ai-is-transforming-work-at-anthropic](https://www.anthropic.com/research/how-ai-is-transforming-work-at-anthropic) |
| **Researched** | 2026-01-29 |

## Overview

Recent empirical research from Anthropic (132 engineers) and METR (16 experienced developers) reveals a paradox: developer productivity gains coexist with a 19% slowdown on individual tasks. The key insight is that AI doesn't universally accelerate—it redistributes effort toward higher-leverage work, behavioral changes in how engineers delegate, and emerging concerns about long-term skill development.

## Key Points

- **Productivity gains are real but task-specific**: Anthropic engineers report 20-50% overall productivity improvements and 14% report >100% gains, yet METR found a 19% slowdown on specific implementation tasks—revealing that macro productivity ≠ micro task speed.

- **Task delegation is the lever**: Engineers successfully delegate low-stakes, easily-verifiable, out-of-domain work (~0-20% fully delegated, requiring active supervision for complexity). This shifts work composition rather than eliminating it.

- **Usage exploded but perception lags**: Claude usage jumped from 28% to 59% of daily work within 12 months. Developers expected 24% speedup but experienced 19% slowdown, yet reported believing they achieved 20% speedup—a significant perception-reality gap.

- **"Full-stack" reshaping is reshaping roles**: Backend engineers building UIs, researchers creating visualizations. Engineers tackle unfamiliar domains, accelerating learning velocity but creating concerns about skill atrophy and reduced "collateral learning" from manual problem-solving.

- **Career trajectory uncertainty**: The shift from code-writing to AI-system management leaves engineers uncertain about long-term prospects. AI replacing colleague interactions reduces mentorship opportunities.

## Technical Details

| Metric | Finding |
|--------|---------|
| **Actual task completion time** | +19% slower with AI (METR) |
| **Developer perception** | Believed +20% speedup after -19% slowdown |
| **Claude usage growth** | 28% → 59% of daily work (12 months) |
| **Overall productivity gain** | +20% to +50% year-over-year |
| **High-gain outliers** | 14% report >100% improvements |
| **Work fully delegated** | 0-20% (rest requires supervision) |
| **New task generation** | 27% of Claude work is previously non-existent tasks |

**Autonomous capability jump**: Claude's ability to use autonomous tool calls increased from 9.8 to 21.2 complexity level over six months.

## Implications

**For architects**: This data breaks the "AI = faster coding" narrative. Real gains come from *task substitution and composition shifts*, not raw speed. Budget for integration friction—AI adds 19% on specific tasks, offset by enabling previously impossible work (exploratory analysis, quality-of-life fixes).

**For hiring/retention**: Developers already perceive role redefinition. Engineers are becoming AI system managers rather than primary code authors. This creates both opportunity (broader domains) and risk (skill depth concerns). Plan knowledge transfer—collateral learning is declining.

**For task estimation**: Don't assume AI reduces task time for individual items. It enables new work, accelerates iteration on complex domains, and shifts time allocation. Factor in the perception-reality gap when communicating productivity gains to non-technical stakeholders.

**Trade-offs to consider**:
- **Breadth vs. depth**: Full-stack capability gains offset by reduced domain specialization
- **Velocity vs. atrophy**: Macro acceleration on projects masks concerns about hands-on problem-solving skills
- **Delegation vs. autonomy**: Only 0-20% work is fully delegated; most requires active engineering oversight

## Related

- [Anthropic research on AI transformation at scale](https://www.anthropic.com/research/how-ai-is-transforming-work-at-anthropic) - Quantitative study of 132 engineers with interview validation
- [METR productivity study (arXiv)](https://arxiv.org/abs/2507.09089) - Controlled experiment on task-level performance with frontier models
- [Fortune article on the slowdown paradox](https://fortune.com/article/does-ai-increase-workplace-productivity-experiment-software-developers-task-took-longer/) - Discussion of the perception-reality gap
- [2025 Stack Overflow Developer Survey on AI](https://survey.stackoverflow.co/2025/ai) - Broader developer sentiment and adoption metrics
