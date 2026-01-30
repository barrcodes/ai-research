# TetrisBench: Benchmarking LLM Game-Playing via Real-Time Code Generation

| | |
|---|---|
| **Source** | Hacker News / TetrisBench |
| **URL** | [tetrisbench.com/tetrisbench/](https://tetrisbench.com/tetrisbench/) |
| **Researched** | 2026-01-26 |

## Overview

TetrisBench is a competitive Tetris benchmark that evaluates LLM capabilities in real-time optimization and adaptive reasoning. Rather than vision-based gameplay, models generate code to evaluate move quality and iteratively refine their scoring functions as game state evolves. Gemini Flash achieves 80% win rate against Opus and 66% average across five tested models, demonstrating cost-effective LLMs can outperform more expensive flagship models on optimization tasks.

## Key Points

- **Win Rates**: Gemini Flash (80% vs Opus, 66% average); Opus 4.5 (68% average); GPT-5.2 (63% average); Grok 4.1 (22%)
- **Methodology**: Models start with an initial optimization function, receive board state, generate placement scores for all valid moves, then continuously refine their evaluation function based on game evolution
- **Competitive Format**: AI-vs-AI matchups with human players able to challenge models; comprehensive game logging captures board states and generated code
- **Novelty Angle**: Tests LLM ability to generate optimization code iteratively, not visual recognition—models adapt their scoring strategies mid-game

## Technical Details

**Benchmark Design:**
- Each model receives current board state as input
- Models execute generated code to evaluate all possible piece placements
- Selected moves update game state and inform strategy adjustments for next turn
- System logs board configurations, code generation, and placement decisions per game

**Performance Context:**
- 181+ games recorded in initial dataset
- Only one human player has beaten Opus at this task
- Models with faster inference (Flash) gain throughput advantage in real-time code generation loops
- Implementation prioritizes LLM evaluation over authentic Tetris gameplay mechanics

## Implications

**What This Reveals:**

1. **Cost-Performance Trade-off**: Gemini Flash's 66% average win rate with lower API costs suggests frontier models may be over-engineered for optimization tasks. Smaller, faster models can outcompete flagship versions when the task rewards iterative refinement and throughput.

2. **Benchmark Limitations**: Tetris here measures code generation and strategy refinement, not game AI ability—this is an optimization/reasoning benchmark using game mechanics as substrate, not a genuine game-playing evaluation.

3. **Model Selection Criteria**: For production systems requiring rapid iterative optimization, Flash-class models warrant reconsideration against traditional flagship choices. Cost-per-inference and token throughput become decisive factors.

4. **Generalization Questions**: Unknown whether Flash's advantage transfers to other optimization domains (scheduling, resource allocation, parameter tuning) or is specific to this code-generation-in-a-loop pattern.

## Related

- [GitHub: models-playing-tetris](https://github.com/rovle/models-playing-tetris) - Earlier multimodal LLM Tetris benchmark using vision/screenshots
- [HN Discussion: TetrisBench AI vs. AI](https://news.ycombinator.com/item?id=46708906) - Community feedback on methodology and implementation
- [Gemini 3 Flash: Speed, Smarts, and Hallucination Rate](https://betterstack.com/community/guides/ai/gemini-3-flash-review/) - Broader Flash model characteristics
