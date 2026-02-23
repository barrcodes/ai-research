# Our First Proof Submissions

| | |
|---|---|
| **Source** | OpenAI |
| **URL** | [openai.com/index/first-proof-submissions/](https://openai.com/index/first-proof-submissions/) |
| **Researched** | 2026-02-23 |

## Overview

OpenAI submitted proof attempts on all 10 First Proof problems—a novel, research-level mathematics challenge designed to evaluate whether AI systems can produce correct, checkable proofs in specialized domains without short-answer verification shortcuts. The model successfully solved at least 5 of 10 problems (4, 5, 6, 9, 10), with several others remaining under expert review. This represents a notable frontier capability in sustained mathematical reasoning and abstraction selection.

## Key Points

- **Novel evaluation framework**: First Proof problems demand end-to-end proof construction in specialized fields—unlike competition math, correctness requires expert scrutiny and domain knowledge. At least two problems were open research questions for years before the authors solved them.

- **5-6 validated proofs from one model iteration**: Problems 4, 5, 6, 9, and 10 are assessed as likely correct by expert feedback. Problem 2 was initially believed correct but was subsequently shown to be incorrect based on official commentary. This represents genuine research-grade reasoning capability, not pattern matching.

- **Training trajectory demonstrates measurable improvement**: A single model improved across training iterations—solving #9 and #10 early, then #6 and #4 within days. This shows the model can develop increasingly rigorous thinking over time, a capability critical for scientific discovery.

- **Human-in-the-loop methodology with transparency**: OpenAI acknowledges limited human supervision: suggesting retry strategies, requesting proof clarifications after expert feedback, and facilitating model-to-ChatGPT verification loops. They note this was "a fast sprint" with "not as clean" a process as ideal. This transparency is architecturally important—they explicitly call for more rigorous evaluation frameworks with First Proof organizers.

- **Context from prior work**: Builds on gold-medal-level IMO performance (35/42, July 2025), GPT-5 science acceleration case studies (November 2025), and physics collaboration where GPT-5.2 proposed a gluon-amplitude formula later formally proved. First Proof is part of a deliberate progression toward increasingly ambitious proof-checking benchmarks.

## Technical Details

**Proof verification challenges**: First Proof problems lack automated correctness checkers. Proof validity hinges on expert domain review—problems span multiple mathematical subfields. This creates a tension between speed-to-evaluation and rigor that OpenAI acknowledges. Their appendix includes "prompt patterns and examples that aim to simulate manual interactions during the process," suggesting heavy use of iterative prompting, clarification, and refinement.

**Model capabilities inferred**:
- Sustained reasoning chains over hours (claimed capability of training focus)
- Abstraction selection and domain-specific problem formulation
- Integration of feedback loops to refine formal statements
- Cross-verification using ChatGPT (another model) for formatting and verification

**Process signal**: The fact that human feedback caused a downgrade of Problem 2 from "likely correct" to "incorrect" is architecturally significant. It reveals model-generated proofs can appear sound to initial inspection but fail under expert scrutiny. This is a critical finding for practitioners: frontier proof systems are still failure-prone in ways that aren't caught by automated metrics.

## Implications

**For evaluation strategy**: First Proof demonstrates that traditional benchmarks (short-answer math, competition performance) miss critical aspects of research-grade reasoning—problem formulation ambiguity, long-horizon reasoning, and abstraction selection. Practitioners building AI-driven research tools should expect significant gaps between high benchmark scores and real expert acceptance.

**For capability assessment**: Solving research-level open problems is a meaningful capability threshold, but the qualification is crucial: with human guidance and feedback loops. This is not autonomous discovery—it's model-assisted research with significant human curation. Production systems should assume similar requirements for reliability.

**For reasoning model training**: The trajectory improvement (solving problems across days of training) suggests that architectural focus on "rigor" and "long-horizon thinking" yields measurable gains on frontier tasks. This is architecturally distinct from scaling to more data or parameters.

**For proof verification systems**: The reliance on expert human review for correctness validation is a bottleneck. Organizations building proof-checking infrastructure should anticipate that AI-generated proofs will require domain specialist review even when models achieve high problem-solve rates.

**Risk consideration**: The transparency about human guidance, feedback loops, and cherry-picking "best attempts" is important context. Claims of "AI solving research problems" require unpacking the human-in-the-loop machinery. Practitioners should be skeptical of isolated success stories without clear methodology documentation.

## Sources

- [OpenAI First Proof Submissions](https://openai.com/index/first-proof-submissions/) - Full article with proof details and quotes from James R. Lee
- [First Proof Challenge](https://1stproof.org) - The official benchmark (referenced in article)
- [OpenAI Proof Attempts PDF](https://cdn.openai.com/pdf/26177a73-3b75-4828-8c91-e8f1cf27aaa0/oai_first_proof.pdf) - Complete proof submissions with appendix on prompt patterns
- [OpenAI Announcement on Twitter](https://x.com/merettm/status/2022517085193277874?s=20) - Initial proof submission announcement (Feb 14, 2026)
