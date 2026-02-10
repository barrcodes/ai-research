# Karpathy Proposes "Agentic Engineering" as Successor to "Vibecoding"

| | |
|---|---|
| **Source** | Andrej Karpathy (X/Twitter) |
| **URL** | [x.com/karpathy/status/2019137879310836075](https://x.com/karpathy/status/2019137879310836075) |
| **Researched** | 2026-02-05 |

## Overview

Karpathy frames "agentic engineering" as the natural evolution of "vibecoding" as LLM agents mature from experimental novelty to production-grade professional workflow. Rather than directly writing code, practitioners now orchestrate autonomous agents while maintaining architectural oversight and quality standards. This represents a paradigm shift in how software is constructed: from craft-level line-by-line coding to meta-level system design and agent supervision.

## Key Points

- **Terminology evolution**: "Vibecoding" (coined Feb 2025) was appropriate for low-capability LLMs used in throwaway demos and explorations. One year later, the field has outgrown it.

- **Two-part definition**: "Agentic" reflects the shift toward agent orchestration rather than direct code authorship (~99% delegation). "Engineering" emphasizes this is a learnable discipline with depth, expertise, and quality standards—not just prompt-and-pray.

- **Quality thesis**: The critical constraint is decoupling agent leverage from software quality compromise. Professional adoption requires rigorous oversight mechanisms, architectural scrutiny, and quality gates that scale with agent autonomy.

- **Dual improvement path**: Progress in 2026 will come from both enhanced model capabilities and improved "agent layer" infrastructure (context management, self-correction, state persistence, autonomous quality validation).

- **Semantic separation**: The naming distinction between "vibecoding" (playful, experimental) and "agentic engineering" (rigorous, professional) serves as a conceptual firewall—preventing conflation of research exploration with production methodology.

## Technical Details

Karpathy's framing implicitly addresses the engineering challenges emerging in agent-driven development:

**Orchestration over authorship**: The shift from 1st-person coding to 3rd-person orchestration changes the cognitive load. Developers must think in higher-level system terms: agent task decomposition, success criteria, failure modes, and recovery strategies. This parallels the abstraction jump from assembly to high-level languages.

**Quality gates at scale**: As agents handle 99% of implementation, the human role concentrates on architectural decisions, oversight checkpoints, and quality validation. This creates new bottlenecks: How do you catch agent hallucinations that compile but are architecturally unsound? How do you validate that generated code matches non-functional requirements?

**Agent infrastructure requirements**: Implied by the discussion is that production agentic engineering depends on solved problems in:
- Long-context session management and state checkpointing
- Deterministic quality validation and automated rollback
- Hierarchical task planning with human-in-the-loop decision points
- Debugging and introspection of agent reasoning chains

## Implications

1. **Role redefinition**: Software engineers are transitioning from implementers to architects/validators. This reframes hiring, training, and career progression. Deep domain expertise and judgment become more critical; keystroke velocity becomes irrelevant.

2. **Quality architecture**: The "agentic engineering" discipline must develop new patterns for ensuring agent-generated code meets architectural intent. This likely includes:
   - Formal specification layers agents can't bypass
   - Staged validation (syntax → type safety → architectural rules → performance bounds)
   - Explicit audit trails for agent decisions
   - Rollback mechanisms with semantic granularity

3. **Skill stratification**: A two-tier system may emerge: "vibecoding" for rapid prototyping and learning, "agentic engineering" for production systems. This creates incentive structures for learning the deeper discipline.

4. **Organizational impact**: Teams will need to invert their code review processes. Rather than reviewing output, they'll be reviewing *agent specifications*—the prompts, constraints, and decision criteria agents operate under. This shifts quality assurance upstream.

5. **Tool evolution**: IDEs and development environments must evolve to provide visibility into agent reasoning, not just code output. Debugging agentic systems requires tracing agent decision chains, not just stack traces.

## Sources

- [Andrej Karpathy's X/Twitter post on Agentic Engineering](https://x.com/karpathy/status/2019137879310836075) - Full retrospective on vibecoding's evolution and the case for "agentic engineering" as the proper terminology for production agent-driven development
- [Reddit discussion - r/singularity](https://old.reddit.com/r/singularity/comments/1qwdzcq/karpathy_proposes_agentic_engineering_as_the/) - Community discussion including perspectives on self-correcting autonomous agents and agent infrastructure requirements
