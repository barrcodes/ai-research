# Inside OpenAI's in-house data agent

| | |
|---|---|
| **Source** | OpenAI |
| **URL** | [openai.com/index/inside-our-in-house-data-agent/](https://openai.com/index/inside-our-in-house-data-agent/) |
| **Researched** | 2026-01-30 |

## Overview

OpenAI built a proprietary conversational data agent to serve thousands of internal users across Engineering, Finance, GTM, and Research—handling 600+ petabytes of data across 70,000+ datasets. The system treats analytics as a multi-turn dialogue problem rather than a one-shot query tool, dramatically reducing insight generation from days to minutes while maintaining queryable context across conversations.

## Key Points

- **Multi-turn reasoning with context preservation** - Unlike traditional SQL generators, the agent maintains conversational state, allows refinement without query restart, and proactively flags ambiguous requests for clarification
- **Trust through "golden SQL" benchmarks** - Implements systematic ground-truth validation (canonical metrics) to catch performance drift and ensure reliability for high-stakes decisions (e.g., CFO requesting revenue figures can't be 99% accurate)
- **Semantic abstraction layer** - Routes natural language to human-curated canonical metrics rather than raw text-to-SQL generation, reducing hallucination risk and enabling verification workflows
- **Built on proven OpenAI primitives** - Leverages Codex, GPT-5, Embeddings API, and Evals API—technologies available to developers worldwide, suggesting reproducibility patterns
- **Organizational governance critical** - Success depends more on data organization, ownership clarity, and documentation quality than algorithmic sophistication; poorly structured data environments see minimal AI benefit regardless of model capability

## Technical Details

The agent operates as an intelligent router in a canonical metrics architecture rather than a pure generative SQL tool. This pattern addresses the fundamental problem: text-to-SQL systems lack sufficient reliability for business-critical analytics where "correct 99% of the time" fails when a CFO needs accurate revenue numbers.

The system uses intermediate semantic layers (knowledge graphs or metric definitions) that humans can verify before execution. This transforms agents from unreliable code generators into high-confidence decision-support systems. The Evals API integrates continuous validation, treating golden SQL datasets as regression test suites.

The infrastructure supports async multi-hop reasoning—decomposing complex questions into sub-queries with verification at each step—rather than attempting single-stage generation.

## Implications

**For practitioners building LLM-based analytics systems:**

- **Avoid direct text-to-SQL in production** - Canonical metrics with human curation are non-negotiable for business analytics; build semantic abstraction layers between users and execution engines
- **Invest in evaluation infrastructure early** - Golden SQL datasets and systematic validation become more critical than model capability improvements; drift detection matters more than marginal accuracy gains
- **Governance precedes tooling** - If your organization lacks clear data ownership, canonical definitions, and quality documentation, adding an LLM agent amplifies chaos rather than enabling insights; fix organizational structure first
- **Multi-turn stateful design is essential** - Single-query tools force users into analytical dead-ends; preserve context and enable refinement loops to match how humans actually think through data problems
- **Internal customers validate the approach** - OpenAI's willingness to run this system on 600+ petabytes suggests confidence in the architecture; if your data volume and user count are comparable, the pattern is likely applicable

## Related

- [Introducing AgentKit](https://openai.com/index/introducing-agentkit/) - OpenAI's framework for building reliable agents with structured reasoning
- [Introducing ChatGPT agent](https://openai.com/index/introducing-chatgpt-agent/) - Research-to-action patterns in conversational agents
- [OpenAI Developers 2025](https://developers.openai.com/blog/openai-for-developers-2025/) - Latest primitives and APIs available for agent development
