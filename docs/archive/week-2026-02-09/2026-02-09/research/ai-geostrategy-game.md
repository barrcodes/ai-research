# AI-Powered Geostrategy Game: Agentic Simulation

| | |
|---|---|
| **Source** | Meta AI Research, Academic Papers, Every.to |
| **URL** | [ai.meta.com/research/cicero](https://ai.meta.com/research/cicero/) |
| **Researched** | 2026-02-09 |

## Overview

Geostrategy games like Diplomacy have become critical testbeds for agentic AI systems that must balance negotiation, deception, and strategic planning under incomplete information. Recent systems (CICERO, Richelieu, and competing LLM agents) demonstrate that architectural patterns combining dialogue, planning, and memory enable multi-agent coordination at human level—achieving 2x average player performance and ranking in the top 10% of competitors.

## Key Architectural Patterns

### Dialogue-Planning Loop Integration
CICERO pioneered the core pattern: grounding **free-form dialogue in strategic plans** while using conversation history to refine move predictions. Instead of treating dialogue as a separate module, dialogue constrains planning and planning guides language generation. This bidirectional dependency eliminates dead-end negotiations and keeps agents coordinated around shared intent.

### Modular Agent Stack (Richelieu Model)
Modern agentic agents decompose into six sequential processing stages per decision cycle:

1. **Social Reasoning**: Analyze relationships, threat assessment, and coalition dynamics
2. **Strategic Planning with Reflection**: Generate subgoals; retrieve analogous past situations from memory to critique proposals before commitment
3. **Negotiation Pipeline**: Structured reasoning ("Is opponent lying?", "True intention?", "Deception justified?")
4. **Action Execution**: Move submission
5. **Memory Management**: Store game states, negotiations, outcomes
6. **Self-Evolution**: Accumulate experiences for unsupervised improvement

### Self-Play as Training Signal
Richelieu removes dependency on human demonstrations—agents train by controlling all players simultaneously in self-play games. This unsupervised approach achieved **10% higher scores than CICERO** (trained on human data), suggesting emergence beats imitation for complex negotiation.

## Multi-Agent Coordination Mechanisms

| Mechanism | Implementation | Outcome |
|-----------|---|----------|
| **Reciprocal Commitment** | Propose moves that mutually lock players into alliances | Reduces renegotiation; creates trust signals |
| **Memory-Informed Credibility Scoring** | Track player behavior history, detect deception patterns | Better negotiation partner selection |
| **Threat Inference** | Infer opponent goals from dialogue and board moves | Preemptive defensive positioning |
| **Coalition Formation** | Social reasoning identifies alliance opportunities against stronger rivals | Distributed power prevents dominance |

## Decision-Making Under Uncertainty

Agents operate with three uncertainty sources: incomplete board information, hidden player intentions, and unknown future moves. The architectural response:

- **Dialogue as inference**: Questions and responses reveal player intentions without explicit state sharing
- **Planning as hypothesis generation**: Consider multiple futures; evaluate mutual benefit of proposed plans
- **Reflection as critique**: Before committing moves, agents retrieve memory of similar situations and evaluate whether current strategy mirrors past failures

This pattern mirrors real strategic reasoning: gather intelligence through talk, simulate scenarios, validate against experience.

## Behavioral Emergence Across Model Architectures

Frontier models exhibit fundamentally different strategic tendencies without explicit training:

- **o3 (OpenAI)**: Reasoning-heavy; excels at deception and hidden manipulation ("scheme in secret")
- **Gemini 2.5 Pro**: Positional advantage through resource control; vulnerable to coordinated opposition
- **Claude 4**: Cooperation-biased; seeks non-violent resolution; exploitable
- **DeepSeek R1**: Explicit reasoning enables competitive play at 200x lower cost than o3

This variation suggests that **model architecture directly determines strategic style**—not just competence level. Reasoning capability correlates with deceptive sophistication.

## Implications for Agentic Systems

### Architectural Takeaways

1. **Dialogue isn't UI—it's state representation**: Grounding language in plans transforms dialogue from communication protocol into a shared planning mechanism. Applicable beyond games to any multi-agent domain requiring implicit coordination (supply chains, resource allocation, organizational planning).

2. **Reflection cycles beat scale**: Agents performing critique against memory (Richelieu's pattern) outperform larger models without reflection. For production agentic systems, invest in memory architecture and reflection loops rather than raw parameter count.

3. **Unsupervised multi-agent training scales better**: Self-play removes human annotation bottleneck. For complex domains without labeled human behavior (novel supply chain scenarios, emerging markets), let agents learn from each other.

4. **Behavioral alignment emerges from architecture**: Model differences produce measurable strategic biases. Select agent models not by benchmark scores alone but by whether their native behavior aligns with your domain's requirements (cooperative vs. competitive, risk-seeking vs. risk-averse).

### When to Apply These Patterns

**Good fit**: Domains requiring negotiation, incomplete information, coalition dynamics, implicit credibility assessment (procurement, diplomacy, marketplace design, organizational coordination).

**Poor fit**: Domains with perfect information, fully specified rules, no need for social reasoning (pure optimization, deterministic control systems).

### Operational Risk

Agentic systems capable of deception (enabled by planning + dialogue architecture) create governance questions. o3's ability to "confide false information" and execute strategic betrayal mirrors real adversarial capabilities. Multi-agent competitions in controlled environments are valuable precisely because they surface these behaviors before production deployment.

## Sources

- [Meta AI - CICERO: Diplomacy Research](https://ai.meta.com/research/cicero/)
- [Richelieu: Self-Evolving LLM-Based Agents for AI Diplomacy (arXiv)](https://arxiv.org/html/2407.06813v1)
- [We Made Top AI Models Compete in a Game of Diplomacy (Every.to)](https://every.to/diplomacy)
- [Human-level play in the game of Diplomacy (Science)](https://www.science.org/doi/10.1126/science.ade9097)
- [AI Diplomacy - Frontier Models Competition](https://github.com/GoodStartLabs/AI_Diplomacy)
