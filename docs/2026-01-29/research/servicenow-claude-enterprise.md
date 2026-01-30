# ServiceNow chooses Claude to power customer apps and increase internal productivity

| | |
|---|---|
| **Source** | Anthropic |
| **URL** | [anthropic.com/news/servicenow-anthropic-claude](https://www.anthropic.com/news/servicenow-anthropic-claude) |
| **Researched** | 2026-01-29 |

## Overview

ServiceNow integrates Claude as the default model for ServiceNow Build Agent, an enterprise coding platform that enables AI-powered application development and workflow automation. The partnership targets 50% reduction in implementation time and expects 4x usage increase within 12 months, demonstrating significant enterprise-scale AI adoption with measurable ROI.

## Key Points

- **Build Agent architecture**: Claude becomes the default reasoning engine in ServiceNow's development platform, supporting both professional developers and citizen developers building enterprise applications
- **Workflow scale impact**: Powers automation of 80+ billion enterprise workflows annually—from security incident resolution to HR onboarding to customer support, with routing and decision-making now enhanced by Claude's reasoning
- **Implementation acceleration**: Targets 50% reduction in deployment time from discussion to production, directly impacting customer time-to-value
- **Internal productivity gains**: ServiceNow's own teams achieved 95% reduction in sales preparation time using Claude-powered coaching; technical teams leverage Claude Code for development and debugging
- **Healthcare specialization**: Claude Opus 4.5 supports industry-specific use cases (research analysis, claims authorization) that reduce processing from days to hours
- **Enterprise governance**: Maintains required access controls, usage monitoring, and compliance for large-scale deployments

## Technical Details

**Model Selection & Performance**
- Claude serves as default in Build Agent, indicating confidence in reasoning depth for code generation and agentic automation
- Claude Opus 4.5 specifically highlighted for healthcare/life sciences domain reasoning

**Deployment Mechanics**
The integration operates at the workflow platform level, not just API-wrapping. ServiceNow's 80B annual workflows now benefit from improved routing, classification, and multi-step decision logic—a strong signal that agentic patterns (sequential reasoning, tool use, state management) are embedded in production pipelines.

**Internal Use Pattern**
Sales coaching tool achieving 95% time reduction suggests structured prompt-driven interfaces around specific enterprise workflows, replicable across departments.

## Implications

**For Enterprise Architects:**
This validates Claude for production agentic systems at scale. The 4x growth projection signals market-wide shift from experimentation to mainstream adoption. If you're evaluating AI models for workflow automation, workflow orchestration at this scale (80B workflows) only works with models that handle multi-step reasoning reliably.

**Trade-offs & Decisions:**
- **Citizen developer enablement vs. code quality**: Build Agent reduces friction for non-engineers, increasing vulnerability surface. Governance controls mentioned but not detailed—audit carefully if adopting similar patterns.
- **Latency sensitivity**: Workflow automation at scale requires fast inference. ServiceNow's selection implies Claude's latency profile supports their SLAs (not disclosed).
- **Cost optimization**: 4x usage growth requires attention to token efficiency. The healthcare use case (days-to-hours claims processing) likely shows strong unit economics, but deployment-specific benchmarking required.

**When to adopt this pattern:**
Suitable for structured workflow automation (incident routing, onboarding, support queues, claims processing). Less suitable for high-frequency, sub-second-latency systems or workloads requiring specialized domain models unavailable in Claude.

## Related

- [ServiceNow Build Agent documentation](https://www.servicenow.com) - Platform details
- [Claude Opus 4.5 capabilities](https://www.anthropic.com/claude) - Model specifications
- [Enterprise agentic workflows](https://www.anthropic.com/research) - Deeper technical exploration
