# Remote Labor Index Updated with Newer Models

| | |
|---|---|
| **Source** | Scale AI / SEAL Leaderboard |
| **URL** | [scale.com/leaderboard/rli](https://scale.com/leaderboard/rli) |
| **Researched** | 2026-02-17 |

## Overview

The Remote Labor Index (RLI) has been updated with performance data from newer frontier models, revealing the critical gap between AI capabilities and real-world freelance work automation. Claude Opus 4.5 now leads with 3.75% automation rate—a marginal improvement over previous best performers. The data underscores that despite rapid model scaling, AI agents remain unable to complete the vast majority of economically grounded freelance projects to professional standards.

## Key Points

- **Claude Opus 4.5 dominates**: Achieves 3.75% automation rate, outperforming all competitors including GPT-5.2 (2.5%), Manus 1.5 (2.5%), and older Claude variants (2.08%)
- **Negligible absolute performance**: Even top agents fail ~96% of projects; this represents the floor of real-world capability testing, not the ceiling
- **Steady relative improvement tracked**: While absolute success rates remain near zero, Elo scores show consistent ranking progression—newer models consistently outperform older ones, enabling sensitivity to incremental gains
- **Generational coverage gap**: Latest generation models (GPT-5.1/5.2, Gemini 3, Opus 4.5) lack comprehensive public test results; prior evaluations dominated by GPT-5, Grok 4, Gemini 2.5 Pro
- **Systematic failure patterns identified**: 45.6% poor quality output; 35.7% incomplete deliverables; 17.6% technical/file format issues; 14.8% visual/logical inconsistencies

## Technical Details

**Evaluation Framework:**
- 240 real freelance projects from Upwork (23 domains), totaling $143,991 economic value
- Mean human completion: 28.9 hours (median: 11.5 hours); average project value $632.60
- Multi-modal deliverables (documents, audio, video, 3D CAD) requiring expert domain judgment
- Manual evaluation via trained reviewers (94.4% inter-annotator agreement)
- 3-point scale: Fails (1), Meets Standard (2), Exceeds Standard (3); Automation Rate = % scoring ≥2

**Agent Testing Methodology:**
- Two execution environments: CLI-based (OpenHands) for code/data; GUI-based (computer-use) for human interaction simulation
- Standardized $30 generation budget per task
- Access to specialized tools: image generation (gpt-image-1), text-to-speech (openai/tts-1), video generation (veo-3.0)
- Evaluation compatibility prompts specifying supported file formats

**Performance Metrics:**
- Automation Rate: Primary metric (% projects ≥ client-acceptable standard)
- Elo Score: Pairwise comparison ranking; human baseline = 1,000 Elo
- Dollars Earned: Total value of successfully completed projects
- Cost Savings: Percentage reduction across portfolio (fails = 0% savings)

**Pockets of Success:**
Audio tasks (sound effects, vocal separation), image generation (logos, ads), report writing, and data retrieval show elevated performance relative to other domains.

## Implications

**For practitioners:**

1. **Agentic automation remains profoundly limited**: The 3.75% success rate on genuine freelance work—despite agents having access to web, code execution, file manipulation, and generative tools—signals that we're measuring a real capability floor. This data is not noise; it reflects systematic gaps in planning, consistency, and domain reasoning.

2. **Elo scores matter more than absolute rates**: Since absolute performance is negligible, relative ranking via Elo becomes the only meaningful measure of progress. This suggests that incremental model improvements (e.g., Opus 4.5 vs. GPT-5.2) are detectable at scale but only visible through comparative methods, not user-facing automation gains.

3. **File handling and multimodality are bottlenecks**: Technical failures (17.6%) and incomplete deliverables (35.7%) dominate failure modes alongside quality issues. The benchmark's requirement for correct file formats and complete outputs is non-negotiable in freelance contexts—partial work has zero economic value.

4. **Evaluation scalability is the constraint**: Manual expert review at 240 projects is expensive and time-consuming. This creates a ceiling on benchmark frequency and coverage. Newer models lack comprehensive results, creating an evaluation lag that obscures current frontier performance.

5. **Domain-specific success is real but narrow**: Success in audio and image generation suggests that narrowly scoped, well-defined output formats (audio waveforms, image files) are easier to automate than complex, multi-step tasks requiring contextual judgment (architectural planning, client communication, iterative refinement).

6. **Implications for agent architecture**: The data suggests that autonomous agents need significant improvement in (a) task planning and decomposition, (b) output validation and error recovery, (c) format compliance, and (d) consistency maintenance across file artifacts—not just raw reasoning capability.

## Sources

- [Scale AI Remote Labor Index Leaderboard](https://scale.com/leaderboard/rli) - Official benchmark with latest model results
- [Remote Labor Index Research Paper](https://arxiv.org/abs/2510.26787) - Formal evaluation methodology and dataset design
- [The AI Performance Gap: Why Frontier Models Fail 96% of Real-World Work](https://www.revolutioninai.com/2026/02/ai-fails-96-percent-remote-labor-index-2026.html) - Analysis of failure modes and implications
- [Remote Labor Index: Measuring AI Automation of Remote Work](https://scale.com/research/rli) - Research details and findings
