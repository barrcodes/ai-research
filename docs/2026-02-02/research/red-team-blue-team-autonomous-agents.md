# Observing Adversarial AI: Red-Team vs Blue-Team OpenClaw Agent Security Audit

| | |
|---|---|
| **Source** | Brane Labs / r/MachineLearning |
| **URL** | [gobrane.com/observing-adversarial-ai-lessons-from-a-live-openclaw-agent-security-audit/](https://gobrane.com/observing-adversarial-ai-lessons-from-a-live-openclaw-agent-security-audit/) |
| **Researched** | 2026-02-02 |

## Overview

Brane Labs conducted a controlled adversarial security audit of autonomous OpenClaw agents with no human intervention once initiated. A red-team agent successfully pivoted from blocked direct attacks to successful indirect execution via JSON payloads containing shell expansion variables embedded in metadata. The critical finding: direct social-engineering attacks are defensible through intent-level reasoning, but indirect execution paths through documents, templates, and memory represent the actual frontier of agent vulnerability.

## Key Points

- **Direct attacks highly defensible**: Red-team's initial "Trojan Horse" approach offering a helpful security pipeline with hidden RCE payload was 100% blocked by the blue-team agent through intent recognition and execution boundary enforcement

- **Indirect execution is the real threat**: Attacker pivoted to asking the agent to "review" a JSON document with malicious shell expansion variables in metadata fields—this payload was successfully delivered and execution flow remains under analysis

- **Lethal Trifecta vulnerability**: The architecture inherently vulnerable when agents have simultaneous access (tools, credentials, APIs), exposure (untrusted inputs from other agents), and agency (freedom to act on those inputs)

- **Memory and persistence vectors most dangerous**: Untrusted documents, templates, and long-term memory poisoning represent the hardest problems in agent security and are where most production systems will fail

- **Observability gap critical**: Current focus on prompt alignment misses the real issue—indirect execution flows, multi-agent propagation, and stateful adversarial scenarios need architectural defenses, not just training

## Technical Details

### Successful Defense Mechanisms

The blue-team agent demonstrated three critical capabilities:
- **Intent-level reasoning**: Evaluated the "why" behind requests, not just surface form syntax
- **Strict execution boundaries**: Refused to treat "helpful" artifacts as inherently trustworthy for execution
- **Stateful adversarial awareness**: Tracked prior failed attacks and anticipated escalation patterns

### Attack Progression

1. **Phase 1 (Blocked)**: Social engineering disguised as helpful collaboration—offer curl | bash wrapped in security pipeline with implicit credential exfiltration request
2. **Phase 2 (Successful)**: Payload delivery via JSON document review with shell expansion variables (`${...}`) hidden in metadata fields, leveraging implicit execution during downstream processing
3. **Remaining analysis**: Final payload outcome under investigation; attack succeeded in delivery regardless of downstream impact

### Architectural Vulnerabilities

The webhook-to-webhook communication pattern creates bidirectional injection surface:
- Agents process untrusted artifacts from other agents as quasi-trusted data
- Downstream tools interpret metadata as configuration/instructions
- Variable expansion happens after deserialization, defeating pre-deserialization validation
- Memory writes from tainted inputs influence all future agent decisions

## Implications

**For practitioners deploying agent systems:**

1. **Treat agent-to-agent communication as fully adversarial** by default, not "collaboration from friendly peers." Isolate trust domains and implement taint tracking on external inputs.

2. **Document execution paths are execution vectors**. JSON, YAML, markdown, and template artifacts are not "data"—they are interpretation targets. Implement goal-lock mechanisms that detect when planned actions drift from original intent.

3. **Persistent memory is your largest attack surface**. OpenClaw agents maintain `.md` memory files that influence all future behavior. Corruption of memory is a persistence vector. Require separate blast radiuses per integration and spending caps per agent autonomy boundary.

4. **Indirect injection will defeat surface-level defenses**. Validation before deserialization is insufficient when shell expansion or variable interpolation happens during tool invocation. Require taint tracking from input to execution point, not just input validation.

5. **Observability is mandatory, not optional**. Current systems lack visibility into why agents made decisions. You cannot defend what you cannot measure. Implement decision audit trails with provenance of all external inputs.

6. **Agent security is not alignment**. Prompt injection literature misses that real failures come from architecture-level vulnerabilities (memory access patterns, credential scoping, multi-agent surfaces). Design for defense in depth: isolated credentials, tool allowlists per operation, separate agent personas per risk boundary.

## Sources

- [Observing Adversarial AI: Lessons from a Live OpenClaw Agent Security Audit](https://gobrane.com/observing-adversarial-ai-lessons-from-a-live-openclaw-agent-security-audit/) - Brane Labs full analysis
- [Reddit discussion thread](https://old.reddit.com/r/MachineLearning/comments/1qsy793/we_ran_a_live_redteam_vs_blueteam_test_on/) - Community context and expert commentary
- Referenced: Christian Schneider's Promptware Kill Chain stages and agentic amplification patterns
- Referenced: Cisco Skill Scanner for open-source validation tooling
