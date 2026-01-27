---
title: Has anyone else noticed Opus 4.5 quality decline recently?
source: Reddit r/ClaudeAI
url: https://www.reddit.com/r/ClaudeAI/
researched_at: 2026-01-26T00:00:00Z
fetch_method: web-search
---

# Has anyone else noticed Opus 4.5 quality decline recently?

## Overview

The Claude community has recently reported significant quality degradation in Claude Opus 4.5, particularly since mid-January 2026. Users are experiencing instruction-following failures, incomplete responses, and apparent model downgrades, accompanied by severely restricted usage limits that have made the service impractical for development work.

## Key Points

- **Instruction-Following Failures**: Since January 14, 2026, Claude Code disregards provided instructions while Sonnet 4.5 continues functioning normally
- **Usage Limits Crisis**: Weekly limits reset on January 8 represent the most restrictive quotas since Opus 4.5 launch in November 2025, with users hitting limits within 10-15 minutes
- **Quality Degradation Reports**: Users describe faulty code generation, terrible memory, increased hallucinations, lazy outputs, and responses feeling like they come from less capable models
- **Silent Model Downgrades**: Silent fallback to Sonnet 4 occurs even when Opus is manually selected, with message "Claude Opus 4 limit reached, now using Sonnet 4"
- **Service Incidents**: January 14 incident confirmed "elevated error rates" for both Opus 4.5 and Sonnet 4.5 due to deployment that temporarily reduced serving capacity
- **Incomplete Responses**: Reports of Claude 4.5 Opus returning incomplete/partial results with degraded output quality

## Technical Details

### Reported Issues

The degradation manifests across multiple dimensions:

1. **Code Generation Quality**: Faulty code suggestions, outdated patterns, and nonsensical outputs that require extensive correction
2. **Context Understanding**: Model appears to forget tasks mid-conversation and struggles with basic coding problems previously breezed through
3. **Instruction Adherence**: Instructions are systematically ignored, with responses not following specified parameters or constraints
4. **Performance Consistency**: Slow response times and occasional severe degradation, inconsistent model performance

### Hypothesis Theories

The community has speculated on causes:
- Cost-driven quantization of the model to reduce inference costs
- Systematic model degradation via silent downgrading mechanisms
- Capacity constraints forcing selective quality reduction
- Infrastructure issues from deployment incidents

### Usage Limit Specifics

- Pre-January 8: Consistent weekly limits since November 2025 launch
- Post-January 8: Dramatically reduced quotas, multiple times more restrictive
- Real-world impact: Developers report becoming unusable within 10-15 minutes of usage
- Silent fallback causing confusion about which model is actually serving requests

## Implications

### For Users

1. **Subscription Value Erosion**: Premium pricing (3x other models) no longer justifies capabilities; alternatives like GPT-5.2, Gemini 3 Pro, and GLM-4.7 becoming attractive
2. **Development Workflow Disruption**: Severely restricted limits make Opus 4.5 impractical for daily development, even at high subscription tiers
3. **Trust Degradation**: Silent model switching without clear notification erodes user confidence in the service

### For Teams and Architects

1. **Vendor Lock-in Risk**: Recent degradation demonstrates critical dependency risks; teams should maintain multi-model strategies
2. **Cost-Quality Trade-offs**: Community reports suggest Anthropic may be optimizing for cost reduction, affecting long-term viability as primary coding partner
3. **SLA Expectations**: Service reliability and quality consistency cannot be assumed with current incident patterns
4. **Evaluation Timing**: Recent changes may not reflect true Opus 4.5 capabilities; baseline testing should account for degradation period

### For Business Decision-Makers

- Premium pricing model unsustainable if quality cannot be maintained or clearly communicated
- Reputation damage in developer community could impact adoption and renewal rates
- Transparent communication about incidents and timeline to resolution is critical

## Community Response

Mixed reactions coexist:
- **Defenders**: Point to Opus 4.5's excellence at complex coding and agentic tasks when working properly, with some calling it "the world's smartest coding AI"
- **Critics**: Describe service as "almost unusable" with usage limits, calling for subscription cancellations
- **Pragmatists**: Adopting multi-model strategies (e.g., Claude Opus 4.5 + GLM-4.7 at 1/7th cost) to hedge against future degradation

The Register reported on this as a surprise usage limit issue affecting developers across the platform in early January 2026.

## Related

- [Issue #20330: Claude Opus 4.5 ignores instructions, significant degradation since Jan 14](https://github.com/anthropics/claude-code/issues/20330) - Primary degradation report with detailed user complaints
- [Issue #19468: Systematic Model Degradation and Silent Downgrading](https://github.com/anthropics/claude-code/issues/19468) - Technical analysis of degradation mechanisms
- [Issue #17084: Opus 4.5 usage limits significantly reduced since January 2026](https://github.com/anthropics/claude-code/issues/17084) - Usage quota crisis documentation
- [The Register: Claude devs complain about surprise usage limits](https://www.theregister.com/2026/01/05/claude_devs_usage_limits/) - Mainstream tech coverage
- [IT Pro: Anthropic's Claude AI chatbot is down](https://www.itpro.com/technology/artificial-intelligence/anthropic-claude-outage-opus-sonnet-down) - Service incident reporting
- [A practical Claude Opus 4.5 review: The good, the bad, and what it means for your business](https://www.eesel.ai/blog/claude-opus-45-review) - Balanced review covering both strengths and weaknesses
- [Claude Code Hits Different - Nathan Lambert](https://www.interconnects.ai/p/claude-code-hits-different) - Technical analysis of Claude Code capabilities
