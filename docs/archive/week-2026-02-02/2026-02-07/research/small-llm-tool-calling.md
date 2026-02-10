# Small LLM Tool-Calling Judgment: CPU-Only Benchmark

| | |
|---|---|
| **Source** | Reddit/GitHub (Mike Veerman) |
| **URL** | [old.reddit.com/r/LocalLLaMA/](https://old.reddit.com/r/LocalLLaMA/) |
| **Researched** | 2026-02-07 |

## Overview

An empirical benchmark testing 11 small LLMs (0.5B–3.8B) on tool-calling **judgment** — not just execution. The core finding: correct inaction > incorrect action. Sub-2B models scored higher through conservative restraint rather than aggressive tool-calling, contradicting intuitions that larger models are always better. The benchmark isolates a critical agent architecture requirement: knowing *when not to call a tool* is more valuable than calling everything that looks actionable.

## Key Points

- **Conservatism beats aggression**: qwen2.5:1.5b (0.800 score) tied for first by declining uncertain prompts; its 3B sibling (0.670) ranked 4th despite higher raw action capability (0.800 vs 0.500) due to false positives on restraint prompts.
- **Keyword matching is a failure mode**: Five models reflexively called `get_weather` when they saw the word "weather," even when explicitly told not to (P11: "Don't check the weather, just find the report") or when weather was already provided (P12).
- **Judgment doesn't scale linearly**: phi4-mini (3.8B, Microsoft) achieved 0.700 action score but failed both hard judgment prompts (P11, P12) with wrong tools — same mistakes as models 5-10x smaller.
- **No sub-4B model passed all judgment tests**: No model reliably handled all three dimensions tested: keyword resistance (P11), negation following, and context awareness (P12 redundancy detection).
- **Execution vs. judgment are decoupled**: llama3.2:3b (US) calls tools on every prompt (failed restraint entirely) but showed better tool *selection* on hard prompts than models that refused to act.
- **1-bit quantization works for JSON structure but not semantics**: BitNet b1.58 produced flawless JSON and handled multi-tool requests, but tool *selection* judgment was poor (0.0 action score on P1-P4).
- **Backend dependency matters**: Same model behaves differently on native-tools vs. raw-prompt backends; this is not a model property but a protocol interaction.

## Technical Details

### Methodology
- **Hardware**: Framework Laptop 13 (AMD Ryzen AI 7 350), 32GB DDR5, **CPU-only** (no GPU). All Ollama models use llama.cpp with Q4_K_M quantization; BitNet uses Microsoft's bitnet.cpp with native 1.58-bit kernels.
- **Inference latency**: 874 ms (qwen2.5:0.5b, fastest) to 8,252 ms (ministral-3:3b, slowest, spiking to 33s on verbose refusals).
- **Test suite**: 12 prompts escalating from easy direct calls (P1-P3) to hard judgment traps (P10-P12). Three runs per prompt; 396 total inference calls.

### Scoring Formula (Novel)
Three independent scores compose a safety-weighted agent score:
- **Action**: correct_tool_calls / 10 actionable prompts. Execution capability.
- **Restraint**: correct_refusals / 2 restraint prompts (P5: meta question, P9: code writing). Policy calibration.
- **Wrong-Tool-Avoidance**: (3 - wrong_tool_count) / 3 for hard prompts P10-P12. Judgment under misleading context.
- **Agent Score** = Action × 0.4 + Restraint × 0.3 + Wrong-Tool-Avoidance × 0.3

This 40/30/30 weighting **penalizes false positives asymmetrically** — a wrong API call is worse than a missed opportunity.

### Hard Prompts (P10-P12) — Architecturally Significant

These expose agentic failure modes:

- **P10** ("Meeting in Bruges, should I train or cycle?"): Correct tool is `get_weather` (implicit reasoning). Models that called `schedule_meeting` failed.
- **P11** ("Don't check weather in Antwerp, find the report"): Correct tool is `search_files`. Explicit negation; calling `get_weather` = instruction-following failure.
- **P12** ("Weather is 8°C and rainy. Schedule indoor meeting?"): Correct tool is `schedule_meeting`. Weather already provided; calling `get_weather` = context-reading failure.

**Key finding**: Most failures were non-errors — models simply didn't call tools (missed, not wrong). But when they did act wrongly, it was always the same error: keyword-triggered `get_weather` calls ignoring negation or redundancy.

### Leaderboard Highlights

**Top 2** (Agent Score 0.800):
- **qwen2.5:1.5b**: Perfect restraint, zero wrong tools, correct on P12 alone.
- **ministral-3:3b**: Identical scores through same conservative strategy.

**Gap to Tier 2**: Action score drops from 0.500 → 0.700 (phi4-mini), but wrong-tool penalties prevent higher overall scores.

**Sub-2B Mini-Leaderboard**: qwen2.5:1.5b leads; qwen2.5:0.5b and smollm2:1.7b tie at 0.640 despite both having 2 wrong tools (keyword trapping on P11, P12).

**Functional floor**: Only 8 of 11 models emitted tool calls at all; 3 (deepseek-r1, gemma3, BitNet base) produced non-standard formats (function syntax, not JSON).

## Implications

### For Agent Architecture

1. **Tool-call gating is the bottleneck, not format generation.** Every model that produced valid JSON could format the basics. Failure is at the decision layer: *should I call a tool here?*

2. **Safety-weighted scoring is essential.** Under pure action-maximization, qwen2.5:3b would rank higher (0.800 vs 0.500 action), but under safety weighting it drops to 4th. This aligns with real deployment: an agent that calls tools cautiously but correctly is more useful than one that acts frequently but wrongly.

3. **Negation and context-awareness are not learned at <4B.** The models that failed P11 and P12 are not "slightly worse" — they're categorically incapable of parsing "don't" or noticing redundancy. This suggests a hard parameter floor, not a smooth scaling curve.

4. **Restraint prompts (meta questions, code writing) are distinguishers.** Perfect restraint on P5 ("What tools do you have?") and P9 ("Write code that uses weather API") separated Tier 1 from Tier 2. Models that treat these as "no tool to call" vs. "trigger-happy" differ fundamentally.

5. **Latency vs. judgment trade-off.** Fastest models (qwen2.5:0.5b at 874 ms) are keyword-triggered. Slowest models that think (deepseek-r1 at 5.7s) can't format output. No universally dominant strategy.

### For Practitioners Building Local Agents

- **Sub-2B for on-device inference**: qwen2.5:1.5b or ministral-3:3b if you prioritize safety (fewer wrong calls); accept 50% action rate in exchange for zero false positives.
- **Up to 3B if you can tolerate ~10% wrong-tool rate**: llama3.2:3b calls tools on everything but has better hard-prompt judgment than smaller models. Not suitable for destructive operations.
- **Avoid 0.5B–1.7B for judgment-heavy agents**: Both qwen2.5:0.5b and smollm2:1.7b failed the same negation tests; no differentiation despite 3x param difference.
- **BitNet evaluation is premature**: 1.58-bit models can format JSON and handle multi-tool requests (unique capability), but tool selection judgment is 0.000 on basic prompts (P1-P4 all missed). Base model needs instruction tuning; 2B-4T variant is still nascent.

### For Tool-Calling API Design

- **Native-tools APIs (Ollama) abstract the decision layer badly.** Models that work well with native tools fail on raw prompts and vice versa. The protocol, not the model, often determines behavior. Standardization is needed.
- **Multi-tool requests are barely supported.** Only BitNet-2B-4T handled both tools in P8 (search_files AND get_weather). Ollama's native-tools API returns only the first call. This is a critical gap for realistic agentic workflows.
- **Prompts need hard examples, not soft ones.** P10-P12 exposed that "ambiguous" prompts (P4) don't differentiate — models either act or don't. Hard prompts with explicit negation and redundancy are necessary to find the floor.

### For Quantization Research

BitNet's 1.58-bit results are architecturally interesting but practically mixed:
- **Strength**: Produces valid JSON on every attempt (zero malformed output). Only model handling multi-tool correctly.
- **Weakness**: Cannot solve basic tool-calling (P1-P4). This suggests 1.58-bit is orthogonal to instruction quality — a 1-bit model trained on tool-calling still might not learn the decision layer.
- **Path forward**: 1-bit models need larger instruction-tuned variants or different training regimens. Parameter count alone is insufficient.

## Sources

- [MikeVeerman/tool-calling-benchmark (GitHub)](https://github.com/MikeVeerman/tool-calling-benchmark) - Full codebase, prompts, and raw results.
- [Reddit discussion: "I tested 11 small LLMs on tool-calling judgment"](https://old.reddit.com/r/LocalLLaMA/) - Author comments on methodology, follow-up questions.
- [Round 4 Report (REPORT.md)](https://github.com/MikeVeerman/tool-calling-benchmark/blob/master/REPORT.md) - Detailed model analysis and statistical breakdowns.
