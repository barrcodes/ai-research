# I Improved 15 LLMs at Coding in One Afternoon. Only the Harness Changed.

| | |
|---|---|
| **Source** | Can.ac |
| **URL** | [blog.can.ac/2026/02/12/the-harness-problem/](https://blog.can.ac/2026/02/12/the-harness-problem/) |
| **Researched** | 2026-02-12 |

## Overview

A benchmark comparing three code edit formats across 16 LLMs demonstrates that changing only the tool interface—not the model itself—yields dramatic performance improvements, up to 10x for weaker models. The "hashline" format outperforms existing "patch" and "str_replace" approaches in 14/16 models while reducing token consumption by 20–30%, establishing that the coding "harness" (tool schemas, error messages, state management) is a critical, often-overlooked architectural lever for LLM coding performance.

## Key Points

- **Hashline format dramatically outperforms alternatives**: By tagging each file line with a 2-3 character content hash instead of requiring exact text reproduction, the model gains stable identifiers without depending on perfect recall of whitespace and content.

- **Weakest models benefit most**: Grok Code Fast 1 improved from 6.7% to 68.3% (+61.6pp, 10x gain). MiniMax doubled performance. Grok 4 Fast reduced output tokens 61% by eliminating retry loops.

- **Format choice matters as much as model quality**: Aider benchmarks showed GPT-4 Turbo swing from 26% to 59% with format change alone. JetBrains' Diff-XYZ and EDIT-Bench research confirmed no single edit format dominates across models.

- **Existing approaches show critical flaws**:
  - Patch format: OpenAI-tuned, fails 50.7% for Grok 4, 46.2% for GLM-4.7
  - String replace: Requires exact whitespace matching, spawns 28+ GitHub issues on "String to replace not found"
  - Cursor's approach: Required training separate 70B model just to merge edits

- **Zero training compute cost**: An 8% improvement in Gemini's success rate exceeds typical model upgrade gains and required only engineering at the tool boundary.

## Technical Details

**Methodology**:
- Benchmark fixture: 180 bugs per run × 3 runs across React codebase mutations (operator swaps, boolean flips, off-by-one errors, identifier renames)
- Models tested: Gemini 3 Flash, Kimi K2.5, GLM-4.7, Claude Haiku/Sonnet, Grok variants, MiniMax, GPT-5.x Codex, DeepSeek V3.2, Devstral, Qwen
- Per-task format: Read file, identify bug via plain English description, run edit, compare output against original
- Hashline encoding: `line-number:hash|content` enables reference-by-identifier without content reproduction

**Hashline Architecture**:
```
11:a3|function hello() {
22:f1| return "world";
33:0e|}
```
Model edits via hash references: *"replace line 2:f1"* instead of reproducing exact content. Hashes serve as integrity check if file changes between reads.

**Performance Gains by Model Type**:
- Strong models: 1-5pp gains (GPT-5.2 Codex, DeepSeek, Claude Sonnet)
- Mid-tier models: 10-20pp gains (Gemini, Kimi, Claude Haiku)
- Weak models: 15-60pp gains (Grok variants, MiniMax, Qwen, Devstral)

## Implications

**For Engineering Teams**:
- Coding agent performance bottlenecks are systematic, measurable, and addressable without model retraining
- Custom harness development yields ROI higher than waiting for model upgrades
- Tool schema design and error handling strategy are architecturally significant—not implementation details

**For LLM Vendors**:
- Vendor lock-in via harness restrictions (Anthropic blocking OpenCode, Google account ban) signals awareness of harness' leverage but represents short-term thinking
- Open-source harness optimization benefits all models; vendor-specific optimization creates permanent moats only for that vendor
- The harness problem represents "the highest-leverage place to innovate right now"

**For Tool Builders**:
- Edit format is not commoditized; it fundamentally shapes what models can express reliably
- The "cool demo to reliable tool" gap is primarily engineering at tool boundaries, not model capability
- Content-hash identifiers solve the core tension between recall fidelity and expressiveness that all prior formats accept as inevitable

**Architectural Takeaway**: When evaluating LLM-powered systems, examine the harness before concluding the model is insufficient. Performance claimed for "GPT-5 vs Opus" discourse often conflates model capability with tool impedance—splitting this concern can recover 5-60pp performance gains with zero retraining cost.

## Sources

- [Can.ac: I Improved 15 LLMs at Coding in One Afternoon. Only the Harness Changed.](https://blog.can.ac/2026/02/12/the-harness-problem/) - Original article by Can Bölük with full benchmark data and code
- [oh-my-pi benchmark code](https://github.com/can1357/oh-my-pi/tree/main/packages/react-edit-benchmark) - Reproducible benchmark fixtures and per-run reports
- [Diff-XYZ benchmark (JetBrains)](https://arxiv.org/abs/2510.12487) - Prior research confirming format-model interaction effects
- [EDIT-Bench](https://arxiv.org/abs/2511.04486) - Real-world editing task performance analysis
