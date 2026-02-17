# Mathematicians Issue a Major Challenge to AI: Show Us Your Work

| | |
|---|---|
| **Source** | Scientific American |
| **URL** | [scientificamerican.com/article/mathematicians-launch-first-proof](https://www.scientificamerican.com/article/mathematicians-launch-first-proof-a-first-of-its-kind-math-exam-for-ai/) |
| **Researched** | 2026-02-11 |

## Overview

Mathematicians have launched "First Proof," a controlled evaluation framework testing whether LLMs can solve genuinely novel mathematical problems sourced from active research. Eleven mathematical experts—including a Fields Medal winner—designed encrypted test cases with solutions locked until February 13, eliminating the possibility that AI systems are merely retrieving training data. This represents a methodological leap beyond previous uncontrolled assessments like IMO benchmarks.

## Key Points

- **Novelty is the core criterion**: Problems are unsolved lemmas from researchers' active work, guaranteeing they don't exist in training corpora. This directly counters the documented pattern of AI "discovering" forgotten papers and claiming originality.

- **Cryptographic verification prevents data contamination**: Solutions remain encrypted until after the submission deadline, closing loopholes where AI could theoretically access answers during generation.

- **Scope is deliberately narrow**: The exam targets small supporting theorems rather than major open problems, reflecting the realistic near-term utility mathematicians see—accelerating "tedious parts of math research" rather than solving Fields Medal-level problems.

- **Rigor as a differentiator**: The structured design with expert validation and transparent methodology marks a departure from previous benchmark games where results lacked accountability.

## Technical Details

**Test Architecture:**
- Problem source: Active research lemmas across multiple mathematical domains
- Expert panel: 11 mathematicians with research credibility
- Submission window: One week for AI systems to attempt solutions
- Verification method: Encrypted solutions decrypt post-deadline, preventing real-time feedback loops
- Validation: Blind assessment by original problem authors

**What Success Means:**
Demonstrated capability at solving non-public, non-training-data problems indicates genuine reasoning ability rather than sophisticated retrieval. This differs fundamentally from benchmarks where contamination is plausible—First Proof explicitly eliminates that escape hatch.

## Implications

**For LLM Architecture:**
This benchmark exposes a critical gap: current models may excel at pattern matching and synthesis but struggle when forced to reason about truly novel problem spaces with no training parallels. Systems claiming "reasoning breakthroughs" will face legitimate scrutiny about whether they're solving or retrieving.

**For Practitioners:**
Use this benchmark as a template for evaluating specialized AI tools. If your tool can't demonstrate performance on controlled, novel test cases, claims about "reasoning" or "problem-solving" warrant skepticism. Unencrypted, post-hoc benchmarks are insufficient for high-stakes applications.

**Decision Points:**
- **When this matters**: Deploying AI for R&D acceleration, technical consulting, research support where novel problem-solving is essential
- **When it doesn't**: Tasks requiring primarily synthesis of existing knowledge or retrieval-augmented generation
- **The trade-off**: Rigorous verification adds complexity but eliminates the most common failure mode in AI evaluation—measuring contamination, not capability

## Sources

- [Scientific American - Mathematicians Launch First Proof](https://www.scientificamerican.com/article/mathematicians-launch-first-proof-a-first-of-its-kind-math-exam-for-ai/) - First Proof benchmark announcement and methodology