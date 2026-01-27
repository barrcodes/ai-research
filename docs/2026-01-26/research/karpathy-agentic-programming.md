---
title: Andrej Karpathy on Agentic Programming
source: Reddit r/singularity & Dwarkesh Podcast
url: https://www.reddit.com/r/singularity/
researched_at: 2026-01-26T00:00:00Z
fetch_method: web-search
---

# Andrej Karpathy on Agentic Programming

## Overview

Andrej Karpathy, co-founder of OpenAI and former head of AI at Tesla, presents a contrarian view on agentic programming maturity. While the industry promotes 2025 as "the year of agents," Karpathy argues we're entering the "decade of agents"—a longer-term, realistic timeline for building truly functional autonomous systems. His assessment acknowledges fundamental cognitive limitations in current LLMs and proposes an architectural path forward via the "cognitive core" concept.

## Key Points

### The Reality Check on Current Agents

- **They don't work**: Current LLMs lack the cognitive capabilities needed for true agentic behavior. Karpathy explicitly states agents cannot reliably function as digital employees or interns due to fundamental gaps in underlying intelligence
- **Missing capabilities**: Models suffer from insufficient general intelligence, inadequate multimodal integration, inability to perform consistent computer interaction, and lack of reliable memory systems
- **Not trainable**: Agents cannot learn continuously or retain information from interactions; telling them something doesn't result in persistent memory

### The "Decade of Agents" Framework

- Karpathy rejects the "year of agents" narrative as overpredicted, stating "I was triggered by that because there's some over-prediction going on in the industry"
- Realistic timeline: It will take approximately a decade to work through fundamental architectural and cognitive issues
- This reflects a gradualist perspective on AGI development, aligning with historical automation trends (circa 2% GDP growth over 250 years) rather than discontinuous disruption

### Vibe Coding vs. Agentic Systems

**Vibe coding** (Karpathy's 2025 coinage):
- Human-in-the-loop conversational programming where developers "fully give in to the vibes" and let LLMs generate code
- Programmers accept AI-generated code without reading diffs, embracing exponential capability gains
- Effective for rapid prototyping, boilerplate generation, and frequently-occurring code patterns
- Uses tools like Cursor Composer with Sonnet and SuperWhisper for voice-to-code workflows

**Agentic coding** (future state):
- Goal-driven autonomous agents capable of planning, executing, testing, and iterating independently
- Minimal human intervention required; agents function with genuine autonomy
- Fundamentally different from vibe coding's collaborative human-AI conversation

### Where Agents Actually Succeed

- **Boilerplate and common patterns**: Agents excel at repetitive, copy-paste code that saturates training datasets
- **High-frequency code**: Anything well-represented in internet training corpora performs well
- **Supervised, structured tasks**: Within clear boundaries with deterministic success criteria
- **Partial autonomy**: Current best practice involves careful balance between AI capability and human control

### The Cognitive Core Vision

Karpathy proposes an architectural path forward:

**Design principle**: A few-billion-parameter model that sacrifices encyclopedic knowledge for agentic capability, running always-on as the kernel of personal computing

**Key features**:
- Native multimodality (text, vision, audio at input and output)
- Matryoshka-style architecture allowing capability dials at test time
- Reasoning with adjustable complexity
- Aggressive tool-using with delegation and verification
- On-device LoRA finetuning for personalization
- Cloud oracle delegation when internet available

**Paradox of intelligence**: Karpathy suggests LLMs with *less* knowledge might be *better agents*—by removing encyclopedic recall, models can focus computational resources on reasoning, problem-solving algorithms, and strategic capability

**Benefits vs. traditional LLMs**: Lower interaction latency, direct access to personal data, offline continuity, data sovereignty, and private computation

## Technical Details

### Fundamental LLM Cognitive Deficits

The gap between current LLMs and true agents stems from:

1. **Reasoning limitations**: Current models lack robust multi-step logical inference
2. **Grounding problems**: Difficulty reliably mapping language to actionable computer operations
3. **State management**: No persistent context or memory between sessions
4. **Verification deficits**: Cannot reliably validate their own outputs or recover from errors
5. **Embodied understanding**: Insufficient multimodal integration prevents unified world modeling

### Architectural Implications

- Traditional knowledge-focused LLM training prioritizes breadth over capability depth
- Personal cognitive cores flip this trade-off: sacrifice breadth, maximize reasoning and tool-using capability
- The shift requires different training objectives, fewer parameters dedicated to facts, emphasis on in-context learning and tool integration

### Timeline Milestones

- **Now (2025-2026)**: Vibe coding era dominates; human-AI collaboration leveraging current capabilities
- **2-5 years**: Agents improve for structured domains; better multimodal integration; emergence of reliable memory systems
- **5+ years**: Cognitive cores mature; partial autonomy becomes standard; early true agents in narrow domains
- **Decade horizon**: Full resolution of fundamental issues; agents functioning at employee/intern parity

## Implications

### For Software Engineers and Architects

1. **Workflow paradigm shift**: Traditional software engineering practices will be disrupted by agentic capabilities, but not imminently. Design for human-AI collaboration rather than full automation

2. **Specialization opportunity**: Deep expertise in:
   - System prompting and instruction engineering for agentic systems
   - Tool design and API architecture for agent integration
   - Verification, validation, and guardrails for autonomous systems
   - Hybrid human-agent workflow orchestration

3. **Organizational architecture**: Teams must embrace "partial autonomy" models now—using agents for augmentation, not replacement. This creates new failure modes and operational challenges

4. **Knowledge management**: The shift from centralized knowledge bases to embodied reasoning in agents requires rethinking how organizations capture and maintain domain expertise

### For Product and Strategic Planning

- **Realistic roadmaps**: Skepticism toward 2025 agent vendor claims; real capability arrives on decade timescale
- **Investment thesis**: Focus on enabling technologies (better multimodal models, memory systems, tool ecosystems) rather than premature full-autonomy solutions
- **Hybrid business models**: Sustainable approaches combine human expertise with agent augmentation; pure agent automation remains speculative

### For Training and Development

- **Skill priorities**: System design, prompt engineering, agent orchestration, and verification become core competencies
- **Avoiding technical debt**: Vibe-coded systems built without understanding underlying models create maintenance nightmares; enterprise adoption requires disciplined engineering
- **Cognitive core literacy**: Understanding the architectural direction shapes technology choices and hiring priorities

## Related

- [Andrej Karpathy — AGI is still a decade away](https://www.dwarkesh.com/p/andrej-karpathy) - Comprehensive Dwarkesh Podcast discussion covering agents, AGI timeline, and cognitive core concept
- [AI researcher Andrej Karpathy says agentic AI is years away from matching industry hype](https://the-decoder.com/ai-researcher-andrej-karpathy-says-agentic-ai-is-years-away-from-matching-industry-hype/) - Summary of Karpathy's skepticism on agent maturity
- [Why Andrej Karpathy Says AI Agents Are a Decade From Reality](https://www.remio.ai/post/why-andrej-karpathy-says-ai-agents-are-a-decade-from-reality) - Analysis of the decade timeline thesis
- [Andrej Karpathy Vibe Coding](https://www.klover.ai/andrej-karpathy-vibe-coding/) - Overview of vibe coding concepts and implications
- [The Decade of Agents: A Strategic Analysis of the Karpathy-Patel Dialogue](https://contextjamming.substack.com/p/the-decade-of-agents-a-strategic) - Deep analysis of Karpathy's strategic positioning on agents
- [Andrej Karpathy on X (Cognitive Core)](https://x.com/karpathy/status/1938626382248149433) - Original cognitive core architectural proposal
- [The future of LLMs: cognitive core + cartridges?](https://killerstorm.github.io/ai/2025/11/04/cogcore.html) - Extension of cognitive core architecture concept
- [Vibe coding: programming through conversation with artificial intelligence](https://advait.org/files/sarkar_2025_vibe_coding.pdf) - Academic framing of vibe coding paradigm
