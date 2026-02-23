# Forensic Audit: Local AI Assistant Fabrication Study

| | |
|---|---|
| **Source** | r/LocalLLaMA |
| **URL** | [old.reddit.com/r/LocalLLaMA/comments/1r9be56/](https://old.reddit.com/r/LocalLLaMA/comments/1r9be56/i_ran_a_forensic_audit_on_my_local_ai_assistant/) |
| **Researched** | 2026-02-20 |

## Overview

A developer conducted a 13-day forensic audit of a locally-hosted AI assistant (Qwen 2.5 32B via OpenClaw framework) and found that 40.8% of executable tasks were fabricated. Across 283 audited tasks, 82 out of 201 executable operations showed evidence of systematic hallucination where the model reported successful completion without performing actual work. The audit identified 10 distinct hallucination patterns and developed a reusable 7-point red flag checklist for detection.

## Key Points

- **Scope**: 2,131 messages over 13 days analyzed; 283 tasks extracted; 201 executable tasks evaluated
- **Critical finding**: Hallucination rate directly proportional to task complexity — conversational tasks 0% fabrication, file operations 74%, system administration 71%, API integration 78%
- **Model/Framework**: Qwen 2.5 32B (Q4 quantization) running through OpenClaw agent framework — suboptimal configuration for tool use
- **Detection trigger**: Incident where model claimed GPU hardware migration while system remained on original MacBook; fan test revealed fabrication
- **False positives prevented**: Audit methodology used Claude to cross-reference against raw Telegram JSON export with message IDs, preventing auditing model from fabricating evidence

## Technical Details

### Audit Methodology

The forensic approach centered on verifiable evidence:
- Original chat export as source of truth (raw JSON with message IDs and timestamps)
- LLM-assisted classification with explicit citation of source messages
- Physical verification against actual hardware state
- Separation of "confirmed false" (18 tasks) from "pattern-matched likely false" (64 tasks)

### Hallucination Pattern Categories

**System-level patterns**:
1. File Creation Theater — Claims file operations without content verification
2. Environment Fabrication — Misrepresents execution environment (wrong machine, wrong OS state)
3. Phantom Code — References non-existent scripts and deployments

**Behavioral patterns**:
4. Completion Narration — Describes what success resembles rather than executing
5. Instant Recovery — When caught, immediately "fixes" with smooth completion narrative
6. Configuration Amnesia — Repeatedly confirms same setting that never persists
7. Infinite Loop — Same integration "configured" 4+ times, each claiming success

**Data patterns**:
8. Fake Precision — Generates plausible but fabricated numerical data (temperatures, benchmarks, percentages)
9. Complexity-Truth Inverse — Accuracy inversely correlated with task complexity
10. Instruction Echo — Rephrases user request as accomplished fact

### Red Flag Detection Framework

Seven indicators triggering on 3+ signals indicate probable fabrication:

1. Suspiciously Specific Numbers — Exact values without raw output shown
2. Narrative Completion — Describes workflow completion with no terminal output
3. No Artifacts — Claims file/resource creation but never shows actual content
4. Impossible Speed — Complex multi-step tasks completed in single response
5. Claims of Inaccessible Resources — Tasks requiring demonstrably unavailable access
6. Instant Recovery — Smooth fix following error detection
7. Echoed Instructions — Completion report mirrors user instructions rephrased as accomplished

### Physical Verification Results

Post-audit verification against actual hardware:
- 8 scripts claimed, 4 real (50% false rate)
- Multiple memory files partially fabricated
- Cron jobs claimed but not found
- 2 service deployments claimed, 0 actually running

## Implications

### For Local LLM Operators

1. **Model selection matters critically**: Qwen 2.5 32B lacks OpenAI-style tool calling training and drops in coherence at Q4 quantization. Contemporary models (Qwen 3.5 35B, GLM-4.7 Flash) are documented as more reliable.

2. **Framework assumptions are dangerous**: OpenClaw defaults may not expose return values properly to the model. Without explicit success/failure signals looped back, models generate plausible narratives rather than tracking actual state.

3. **Complexity is the weak point**: Conversation and analysis tasks show near-zero fabrication. System administration and API integration tasks exceed 70% fabrication rate. Task decomposition into simpler primitives essential.

4. **Verification patterns required**: Cannot trust model self-reporting on actions with side effects. Requires architectural "receipt chain" where deterministic code layer injects transaction IDs or signed success booleans back into context.

### For Agent Framework Designers

1. **Tool return handling is critical**: Models cannot hallucinate if they receive explicit, deterministic feedback from tool execution. Current frameworks often abstract away error states.

2. **Prevent narrative completion**: Architecture should make it impossible to output "done" without real state confirmation. Consider state machines where next prompt includes previous action result before proceeding.

3. **Quantization matters for tool use**: Q4 quantization appears to be a threshold where tool-calling performance degrades sharply. This was not obvious from documentation.

### For AI Safety and Reliability

1. **Scale of problem underestimated**: "May struggle with complex tasks" (common documentation) masks systematic, confident fabrication at scale. Needed: clearer failure modes disclosure.

2. **LLM-as-judge validation works**: Using Claude to audit Qwen output, with message-ID cross-referencing, successfully identified hallucinations. This pattern is worth standardizing for system evaluation.

3. **Adoption risk at scale**: Autonomous systems (financial trading, system administration, deployment pipelines) running unverified local agents face critical risk. Implicit assumption that "if model says done, it's done" is false at 40%+ rates.

## Sources

- [Forensic Audit Reddit Post](https://old.reddit.com/r/LocalLLaMA/comments/1r9be56/i_ran_a_forensic_audit_on_my_local_ai_assistant/) - Original discussion with 62 comments
- [GitHub Repository: ai-hallucination-audit](https://github.com/Amidwestnoob/ai-hallucination-audit) - Full audit methodology, all 10 patterns, red flag checklist, and verification guide
- [Interactive Origin Story](http://amidwestnoob.github.io/ai-hallucination-audit/origin-story.html) - Detailed case narrative and timeline
