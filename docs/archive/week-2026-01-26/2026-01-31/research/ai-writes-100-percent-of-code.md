# Top engineers at Anthropic and OpenAI: AI now writes 100% of our code

| | |
|---|---|
| **Source** | Fortune |
| **URL** | [fortune.com/2026/01/29/...](https://fortune.com/2026/01/29/100-percent-of-code-at-anthropic-and-openai-is-now-ai-written-boris-cherny-roon/) |
| **Researched** | 2026-01-31 |

## Overview

Engineers at frontier AI labs (Anthropic, OpenAI) are now generating 100% of production code through AI models, fundamentally reshaping development workflows. Boris Cherny ships 20+ PRs daily—each entirely AI-authored—while teams focus on architecture and validation rather than implementation. This represents a critical inflection point: development becomes orchestration rather than programming.

## Key Points

- **100% AI generation at labs**: Anthropic's Claude Code generates essentially all code within the organization; similar patterns at OpenAI and research teams
- **Dramatically different from industry**: Microsoft and Salesforce report ~30% AI code; academic analysis found ~29% on GitHub—the 100% figure is specific to frontier labs with proprietary models
- **Developer role shift**: Engineers transition from writing to reviewing, validating, and directing—Cherny ships 22+ PRs/day (all AI-generated) vs. traditional manual authorship
- **Hiring restructure underway**: Anthropic now recruits "mostly generalists rather than specialists," as implementation skills become commoditized
- **Timeline prediction**: CEO Dario Amodei forecasts 6-12 months until AI handles "most or all of software engineering work from start to finish"

## Technical Details

**Workflow transformation**: Instead of writing code, engineers compose prompts and validate outputs. Cherny uses Claude for code generation, PR authorship, and non-coding tasks (project management, Slack automation).

**Bootstrapping evidence**: Anthropic built Cowork (file management agent) in ~1.5 weeks "largely using Claude Code itself"—demonstrating recursive capability where AI-generation infrastructure self-improves.

**Remaining friction points** (per Andrej Karpathy):
- Subtle conceptual errors despite overall correctness
- Over-engineered solutions that could be simpler
- Dead code accumulation requiring cleanup

**Quality gap**: Lab-specific. Frontier models (Opus 4.5) paired with domain context exceed 100% generation viability. Outside labs, 30% rates suggest cost-benefit still favors human authorship for critical paths.

## Implications

**For your organization**:
1. **Hiring and skill development** become misaligned with current training pipelines. Specialization (deep systems knowledge, language mastery) decouples from output value. Generalists + strong architecture sense outweigh traditional deep IC track.

2. **Code review scales differently**. 20+ PRs/day implies automated validation systems must replace line-by-line review. Expect shift to semantic/property testing and threat modeling rather than syntax/style enforcement.

3. **Architectural decision-making compounds in value**. If implementation is free, cardinality of viable designs explodes. Frameworks for "good enough" architecture vs. premature optimization invert—you can now validate multiple approaches in hours.

4. **Organizational structure risk**: If 6-12 month prediction holds, teams optimized for serial PR cycles will face disruption. Early adopters gain leverage; incumbents face churn. Model access and fine-tuning become strategic moats.

5. **Validation burden increases**. With "subtle conceptual errors" persisting despite surface correctness, you need property-based testing, formal verification where critical, and adversarial review practices currently rare at scale.

## Sources

- [Fortune: Top engineers at Anthropic and OpenAI: AI now writes 100% of our code](https://fortune.com/2026/01/29/100-percent-of-code-at-anthropic-and-openai-is-now-ai-written-boris-cherny-roon/) - Primary reporting on AI code generation adoption at frontier labs
