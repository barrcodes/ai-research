# Anthropic Drops Flagship Safety Pledge

| | |
|---|---|
| **Source** | TIME Magazine |
| **URL** | [time.com/7380854/exclusive-anthropic-drops-flagship-safety-pledge](https://time.com/7380854/exclusive-anthropic-drops-flagship-safety-pledge/) |
| **Researched** | 2026-02-25 |

## Overview
Anthropic has fundamentally weakened its 2023 Responsible Scaling Policy (RSP), removing binding commitments to halt model training if safety mitigations cannot be guaranteed beforehand. The updated policy replaces hard tripwires with discretionary delays, making safety escalation continuous rather than step-gated.

## Key Points
- **Original commitment eliminated**: The 2023 RSP promised to pause training without adequate pre-verified safety measures—this binary constraint is gone
- **New policy is discretionary**: Model scaling now requires only that Anthropic match competitors' safety efforts and "delay" (not halt) if leadership judges both that it leads the AI race AND catastrophic risks are significant—a two-gate requirement rather than one
- **Transparency substitutes for prevention**: The company replaces binding safety thresholds with enhanced disclosure obligations and risk reporting
- **Competitive framing**: Leadership argues unilateral pausing would be counterproductive—rivals would accelerate unmitigated development, making the world less safe overall

## Technical Details
The RSP overhaul removes **automatic tripwires** that previously acted as hard stops. Under the old framework, safety verification had to precede training. The new framework inverts this: training proceeds contingent on matching competitor safety postures and subjective catastrophic risk assessment by leadership.

The policy shifts from **ex-ante constraints** (verify first, then scale) to **ex-post transparency** (scale, then disclose risks). This creates a narrative justification for incremental risk accumulation: each step remains justified if competitors aren't ahead and catastrophic risk isn't judged "significant" at that moment.

## Implications
This represents a significant architectural decision affecting how safety governance operates in large AI labs:

- **For practitioners building on Claude/Anthropic systems**: Safety guarantees are now based on competitive parity, not absolute thresholds. Plan for ongoing capability expansion without predetermined safety gates.
- **For organizations relying on AI safety commitments**: Binding pledges are replaced with soft commitments. Due diligence must focus on disclosed risk assessments rather than formalized constraints.
- **For the broader field**: The reversal signals that society lacks mechanisms to enforce safety pauses. Companies face race dynamics that make unilateral safety constraints economically untenable. This matters architecturally because it means safety must be **embedded in design and monitoring** rather than enforced via policy checkpoints.
- **For governance frameworks**: Binary safety gates are deprecated in favor of continuous risk disclosure. This shifts burden from prevention to transparency—implicitly betting that visible risk triggers corrective action faster than constraints do.

The decision reflects a fundamental tension: commitment to safety as a formal architectural property versus pragmatic competitive survival. Anthropic chose the latter, gambling that transparent risk disclosure creates sufficient pressure to prevent catastrophic scaling.

## Sources
- [TIME Magazine - Exclusive: Anthropic Drops Flagship Safety Pledge](https://time.com/7380854/exclusive-anthropic-drops-flagship-safety-pledge/) - Original reporting on RSP policy revision
