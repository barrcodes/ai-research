# Claude Code Daily Benchmarks for Degradation Tracking

| | |
|---|---|
| **Source** | MarginLab |
| **URL** | [marginlab.ai/trackers/claude-code](https://marginlab.ai/trackers/claude-code/) |
| **Researched** | 2026-01-29 |

## Overview

MarginLab operates a continuous performance monitoring system for Claude Code, tracking daily degradation against a contamination-resistant subset of SWE-Bench-Pro. The system employs statistical significance testing to distinguish signal from noise, currently detecting a 4.1% performance drop over 30 days that crosses the significance threshold (p < 0.05).

## Key Points

- **Current baseline**: 58% historical pass rate; 50% daily (N=50), 53% weekly, 54% monthly aggregates
- **Detected degradation**: -4.1% over 30 days statistically significant; -4.8% weekly and -8.0% daily changes within normal variance
- **Measurement rigor**: Uses Bernoulli random variable modeling with 95% confidence intervals; weekly/monthly aggregation for stability
- **Coverage scope**: Contaminat-resistant SWE-Bench-Pro subset evaluated against latest Claude Code CLI with Opus 4.5
- **Significance thresholds**: Daily (±14%), weekly (±5.6%), monthly (±3.4%) variance bands distinguish real degradation

## Technical Details

The tracker applies statistical hypothesis testing rather than raw pass rate comparisons. With 50 daily evaluations, daily fluctuations of ±14% fall within expected noise, but the cumulative 655-evaluation monthly sample constrains variance to ±3.4%, making the observed 4.1% decline statistically significant.

**Key measurement design choice**: Results reflect actual Claude Code user experience without synthetic harnesses or optimized test configurations. This produces representative but noisier signals—justifying the layered aggregation approach (daily/weekly/30-day) for actionable insights.

## Implications

**For practitioners**: This baseline establishes quantified expectations for Claude Code reliability. The monthly-scale degradation suggests cumulative drift beyond normal variance—worth investigating for root causes (model updates, test dataset drift, or infrastructure changes).

**For architects**: The significance testing framework prevents false alarms from daily noise while catching genuine regressions. However, a -4.1% drop from 58% baseline (now ~54% monthly rate) remains within acceptable bounds for optional tooling; your acceptance threshold depends on task criticality.

**Trade-off**: Continuous monitoring trades analytical overhead for early degradation detection. For production workloads, establish your own significance thresholds and failure budgets rather than relying on zero-defect expectations.

## Related

- [SWE-Bench](https://www.swebench.com/) - Software engineering benchmark suite used for evaluation
- [Claude Code documentation](https://claude.ai/docs) - Official product guidance
