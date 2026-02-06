# Trusted Access for Cyber

| | |
|---|---|
| **Source** | OpenAI |
| **URL** | [openai.com/index/trusted-access-for-cyber](https://openai.com/index/trusted-access-for-cyber/) |
| **Researched** | 2026-02-06 |

## Overview

OpenAI announced Trusted Access for Cyber on February 5, 2026—an identity and trust-based gating framework designed to restrict GPT-5.3-Codex's autonomous cybersecurity capabilities to verified defenders while blocking malicious actors. This represents the first frontier model reaching "High" capability status under OpenAI's Preparedness Framework for cybersecurity, triggering unprecedented access controls and a $10 million defensive acceleration fund.

## Key Points

- **Capability Threshold**: GPT-5.3-Codex achieves 77.6% accuracy on Capture The Flag challenges and can operate autonomously for hours or days on complex security tasks—a capability tier that triggers mandatory gating.

- **Three-Tier Access Model**: Individual identity verification at chatgpt.com/cyber for standard users; organization-wide trusted access for vetted enterprise security teams; invite-only researcher programs for advanced defensive work requiring enhanced permissions.

- **Threat Model Recognition**: OpenAI acknowledges the dual-use problem explicitly—the same capabilities enabling sophisticated vulnerability detection could facilitate code-based attack development if deployed without controls.

- **Safety Stack**: Dual-use safety training causes explicit refusal of clearly malicious requests (credential theft, malware creation, unauthorized testing); automated classifiers monitor for cyber abuse patterns; threat intelligence integration detects misuse signals across access tiers.

## Technical Details

**Verification Criteria** (non-exhaustive): The framework evaluates organizational affiliation, security research history, CVE disclosures, and professional certifications, though OpenAI has kept full evaluation criteria opaque.

**Monitoring Scope**: Automated classifiers operate on cyber activity signals; real-time threat intelligence correlates across all access tiers; safety training handles refusing clearly malicious requests rather than ambiguous ones.

**Performance Baseline**: GPT-5.3-Codex demonstrates "markedly higher performance" on coding benchmarks, particularly in writing, debugging, testing, and reasoning about code—capabilities that create both defensive and offensive utility.

## Implications

This framework establishes a precedent for conditional model release when capabilities cross defined risk thresholds. Key trade-offs for practitioners:

- **Access Friction**: Verification delays during active incidents may hamper emergency response; independent researchers without institutional affiliation face equity concerns versus enterprise teams.

- **Capability Asymmetry**: Organizations with approved access gain automated defense capabilities while malicious actors must develop equivalent tools independently—a meaningful but not insurmountable barrier.

- **Preparedness Framework Signaling**: The "High" designation telegraphs that future frontier models may face similar or stricter controls, reshaping expectations for AI capability deployment in dual-use domains.

- **Automation Risk**: Unlike human-assisted security tools, 24/7 autonomous operation increases scale of both legitimate and illegitimate outcomes—verification addresses actor intent, not inherent capability risk.

The framework prioritizes verified identity and organizational trust over technical isolation, betting that social controls plus monitoring can contain frontier cybersecurity capabilities. This approach trades deterrence (delaying malicious deployment) against friction (slowing legitimate defense).

## Sources

- [Introducing Trusted Access for Cyber - OpenAI](https://openai.com/index/trusted-access-for-cyber/)
- [OpenAI Trusted Access for Cyber Unveiled: 7 Defense Shifts - Adwait X](https://www.adwaitx.com/openai-trusted-access-cyber-gpt-5-3-codex/)
- [OpenAI Launches Trusted Access to Strengthen Cybersecurity Protections - GBHackers](https://gbhackers.com/openai-launches-trusted-access/)
- [OpenAI's new model leaps ahead in coding capabilities—but raises unprecedented cybersecurity risks - Fortune](https://fortune.com/2026/02/05/openai-gpt-5-3-codex-warns-unprecedented-cybersecurity-risks/)
