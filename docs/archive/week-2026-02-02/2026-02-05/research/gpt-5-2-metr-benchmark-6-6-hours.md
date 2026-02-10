# GPT-5.2 Achieves 6.6-Hour METR Time Horizon Benchmark

| | |
|---|---|
| **Source** | METR / r/singularity |
| **URL** | [old.reddit.com/r/singularity/comments/1qw2plp](https://old.reddit.com/r/singularity/comments/1qw2plp/gpt52_high_sets_highest_mark_on_metr/) |
| **Researched** | 2026-02-05 |

## Overview

GPT-5.2 high has set a new benchmark on METR's 50%-time-horizon metric at 6.6 hours, establishing the longest demonstrated task-completion window for any frontier model. This represents a significant acceleration in the exponential trend METR has been tracking—the doubling time for task-completion duration appears to be compressing from the historical 7-month rate, with recent models (GPT-5, Gemini 3, Claude 4.5) showing substantially faster capability growth than the baseline curve would predict.

## Key Points

- **Record Performance**: GPT-5.2 (high reasoning effort) achieves 6.6 hours at 50% success rate, surpassing Claude Opus 4.5 (2.5 hours) and Gemini 3 Pro (2.0 hours). At 80% threshold, the gap widens even further (55 minutes vs Gemini/Opus at 43-44 minutes).

- **Acceleration Beyond Linear Extrapolation**: The historical METR trend predicted ~7-month doubling times. Recent 2024-2025 models are departing significantly from this curve, suggesting either a genuine acceleration or successful scaling of architectural/training innovations.

- **Evaluation Constraints Becoming Relevant**: The xHigh variant evaluation was not completed within the publication window—computational cost prevents full assessment. GPT-5.2 xHigh would likely extend further, but 5.3 availability may preempt that testing. This hints at infrastructure scaling limits becoming the binding constraint.

- **80% Threshold Breakthrough**: The 55-minute result at 80% success rate crosses into sub-hour territory for multi-step software engineering tasks, which is architecturally significant—it means consistent autonomous tool use and error correction within a bounded session.

- **Context Window & State Management**: Commentary from experienced practitioners notes that multi-hour task completion depends heavily on coherent context management. The jump from ~1-2 hours to 6+ hours requires explicit infrastructure for memory coherence, not just raw reasoning capability.

## Technical Details

**METR Methodology**: Tasks are calibrated against human expert completion times. A model's "time horizon" is derived by fitting logistic regression to success rates across the task distribution, then finding the task duration where the model achieves the specified success threshold (50% or 80%). Tasks span software engineering (bug fixes, training classifiers, buffer overflow exploitation, model training) and structured reasoning.

**Key Architectural Implications**:
- The reasoning effort levels (standard vs. high) map to real infrastructure differences in state management and context compression strategies
- Multi-hour tasks surface context fragmentation as the critical bottleneck; raw compute and instruction-following capability are insufficient
- Evaluation cost scales dramatically with task duration (GPT-5.2 high took ~26x longer to evaluate than Opus 4.5 per task), creating measurement barriers for xHigh variants

**Trend Analysis**: If the 2024-2025 acceleration holds (estimated 3-4 months doubling vs. 7-month historical), month-long autonomous task completion arrives 2-2.5 years earlier than historical extrapolation suggested. Even conservative estimates suggest sub-month horizons by 2027-2028.

## Implications

For practitioners building agentic systems:
1. **Session architecture matters more than model selection in the 2-6 hour range**: Infrastructure for coherent state preservation (checkpointing, context resumption) is now the primary lever for extending task horizons, not just waiting for larger context windows.
2. **Tool-use reliability has crossed a threshold**: The consistent 50%+ success rates on hour-scale tasks means autonomous agents can now operate unsupervised on computer use and code modification—infrastructure like tool error handling and self-correction become critical.
3. **Cost-capability trade-off is steepening**: Evaluation cost and inference latency grow nonlinearly with reasoning effort and task duration. Production deployments will need careful calibration of reasoning budget to task complexity.

For forecasting and capability assessment:
1. **Historical models are inadequate**: Pre-2024 trend lines systematically underestimate recent progress. The shift toward exponential acceleration (as opposed to linear improvement) changes deployment timelines by years.
2. **Evaluation infrastructure is becoming the bottleneck**: The inability to complete xHigh evaluations in reasonable timeframes suggests measurement gaps are widening faster than capability improvements. Benchmarking will need innovation in evaluation efficiency or task generation.
3. **Month-scale autonomy is no longer theoretical**: At current acceleration rates, systems capable of independent execution on month-long projects arrive within the decade with high confidence. Risk and governance frameworks should calibrate accordingly.

## Sources

- [METR Blog: Measuring AI Ability to Complete Long Tasks](https://metr.org/blog/2025-03-19-measuring-ai-ability-to-complete-long-tasks/) - Detailed methodology, sensitivity analyses, and extrapolation framework
- [METR Time Horizon Paper (arXiv:2503.14499)](https://arxiv.org/abs/2503.14499) - Full technical documentation
- [Reddit Discussion: r/singularity](https://old.reddit.com/r/singularity/comments/1qw2plp/gpt52_high_sets_highest_mark_on_metr/) - Community commentary on implications and comparison with Claude 4.5
