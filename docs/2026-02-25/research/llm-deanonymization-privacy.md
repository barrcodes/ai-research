# Large-Scale Online Deanonymization with LLMs

| | |
|---|---|
| **Source** | arXiv + Reddit Discussion |
| **URL** | [arxiv.org/abs/2602.16800](https://arxiv.org/abs/2602.16800) |
| **Researched** | 2026-02-25 |

## Overview

Researchers from MATS, ETH Zurich, and Anthropic demonstrate that modern LLMs can perform large-scale deanonymization with high precision. Given only pseudonymous online profiles and conversational snippets, their LLM-powered agent can re-identify users across platforms (Hacker News, Reddit, LinkedIn) with 68% recall at 90% precision—far exceeding classical methods. The practical obscurity that once protected anonymous online activity has eroded.

## Key Points

- **Scalable attack pipeline**: LLMs extract identity-relevant features from unstructured text, search via semantic embeddings, and verify matches to reduce false positives—no manual feature engineering required
- **Performance gap**: Achieved 68% recall at 90% precision vs. near 0% for non-LLM baselines across three cross-platform linking scenarios
- **Practical validation**: Successfully identified 9 of 125 Anthropic interview participants using only their conversation data
- **Multi-platform scope**: Demonstrated across Hacker News, Reddit, LinkedIn, and anonymized interview transcripts from known ground-truth datasets
- **Data-agnostic approach**: Works directly on raw user content without requiring structured metadata—a fundamental shift from prior deanonymization work requiring handcrafted features

## Technical Details

**Attack Architecture:**
1. **Feature extraction**: LLMs analyze unstructured text (posts, comments, interview transcripts) to infer identity-relevant signals (location, profession, interests, behavior patterns)
2. **Candidate search**: Semantic embeddings enable efficient retrieval of potential matches from large candidate pools (tens of thousands of profiles)
3. **Verification & ranking**: LLM-based reasoning over top candidates reduces false positives and confirms matches with confidence scores

**Evaluation Datasets:**
- Hacker News ↔ LinkedIn cross-referencing using platform mentions
- Reddit movie discussion communities (same-user multi-community identification)
- Temporal splitting (split single Reddit user histories into two pseudonymous profiles)

**Performance Baselines:**
- Classical approaches (embeddings alone, metadata-only): 0% recall
- Gradient-based + semantic methods: < 5% recall
- LLM reasoning pipeline: 68% recall at high precision (90%+)

## Implications

**For Privacy Architecture:**
- Pseudonymity without strong structural separation (different platforms, temporal gaps) is no longer reliable protection
- Current threat models for online anonymity systems must assume capable adversaries with LLM access
- Text-based anonymity (writing style, behavioral patterns) can be reverse-engineered at scale faster than human investigation

**For Practitioners & Organizations:**
- User privacy controls must account for LLM-derived inference, not just explicit data visibility
- Data minimization becomes critical—any persistent writing sample across contexts creates deanonymization surface area
- Interview/feedback systems relying on anonymity for honest responses face degraded protection
- Redaction strategies must be LLM-aware (style homogenization, structural anonymization, not just PII removal)

**For Threat Modeling:**
- Assume motivated adversaries have LLM+web search capabilities and can correlate features across platforms
- Practical obscurity (scattered, low-signal presence) no longer holds as a defense mechanism
- Re-identification scales linearly; targeting thousands of individuals is now computationally feasible

**For Future Defenses:**
- Structural anonymity (separate accounts, pseudonymous infrastructure) with disciplined usage patterns is more robust than content-based anonymity
- Homogenization of writing style/vocabulary across contexts adds friction to LLM-based inference
- Privacy-preserving alternatives (trusted infrastructure, cryptographic anonymity) may be necessary for high-stakes use cases

## Sources

- [Large-scale online deanonymization with LLMs](https://arxiv.org/abs/2602.16800) - arXiv:2602.16800, Lermen et al., February 2026
- [Large-Scale Online Deanonymization with LLMs](https://simonlermen.substack.com/p/large-scale-online-deanonymization) - Simon Lermen's Substack writeup
- [r/MachineLearning Discussion](https://old.reddit.com/r/MachineLearning/comments/1reee40/r_largescale_online_deanonymization_with_llms/) - Reddit discussion thread
