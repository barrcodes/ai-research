# Agent Skills in the Wild: An Empirical Study of Security Vulnerabilities at Scale

| | |
|---|---|
| **Source** | arXiv |
| **URL** | [arxiv.org/abs/2601.10338](https://arxiv.org/abs/2601.10338) |
| **Researched** | 2026-01-26 |

## Overview

This empirical study analyzed 31,132 agent skills from major marketplaces and found that 26.1% contain at least one security vulnerability. The research identifies a critical architectural flaw: agent skills execute with implicit trust and minimal vetting despite bundling executable code that can access system resources, exfiltrate data, and escalate privileges. The authors developed SkillScan, a detection framework combining static analysis with LLM-based semantic evaluation, achieving 86.7% precision in identifying vulnerabilities across 14 distinct attack patterns.

## Key Points

- **Vulnerability Prevalence**: 26.1% of 31,132 skills contain vulnerabilities; 5.2% exhibit high-severity patterns suggesting malicious intent
- **Most Common Threats**: Data exfiltration (13.3%) and privilege escalation (11.8%) dominate; supply chain risks present across dependency chains
- **Architecture Risk**: Skills bundling executable scripts are 2.12x more likely to contain vulnerabilities than instruction-only alternatives
- **Four Attack Categories**: Prompt injection, data exfiltration, privilege escalation, and supply chain risks span the vulnerability landscape
- **Detection Framework**: SkillScan uses three-stage hybrid approach (static analysis → LLM-Guard scanning → Claude 3.5 Sonnet classification)

## Technical Details

### Vulnerability Patterns

| Category | Percentage | Key Pattern | Example |
|----------|-----------|------------|---------|
| Data Exfiltration | 13.3% | E2: Environment variable harvesting | `os.environ` accessing API keys |
| Privilege Escalation | 11.8% | Permission elevation without user consent | `sudo` commands, permission changes |
| Prompt Injection | Variable | P2: Malicious markdown instructions | Silent data exfiltration in comments |
| Supply Chain | Variable | SC2: Remote code execution | `curl \| bash` constructs |

### SkillScan Detection Methodology

**Stage 1: Static Analysis** — Regex and keyword matching on skill code and instructions; detects HTTP requests, environment access, privilege escalation commands.

**Stage 2: LLM-Guard Scanning** — Ten modular security scanners targeting prompt injection, hardcoded secrets, obfuscation, and harmful content patterns.

**Stage 3: Hybrid Classification** — Claude 3.5 Sonnet evaluates flagged skills with full context, assigning confidence scores and evidence. Achieves 86.7% precision and 82.5% recall on validation data.

### Attack Scenarios

Skills harvest credentials via environment variables, exfiltrate project structure through silent comments, download and execute remote code via package managers, and transmit data to attacker-controlled external URLs—all without explicit user consent mechanisms.

## Implications

**For Practitioners**: Current agent skill architectures operate under a trust-execute model incompatible with security requirements. Organizations deploying agentic systems must:

1. **Implement Capability-Based Permissions** — Restrict skill access to necessary resources (file read-only vs. write, specific API endpoints)
2. **Mandatory Pre-Deployment Vetting** — Establish security review gates before marketplace distribution; static analysis is insufficient
3. **Explicit Consent Mechanisms** — Close the gap between what users approve and what skills actually execute; require declarative permission models
4. **Supply Chain Auditing** — Scan skill dependencies and transitive code execution paths

**Decision Point**: Instruction-only skills show substantially lower vulnerability risk (2.12x factor). Prefer restricting custom skills to LLM-interpretable instructions where possible rather than bundled Python/executable code.

**Architectural Trade-off**: Implicit trust enables rapid capability extension but creates exploitable attack surface. The urgency is genuine—5.2% of deployed skills show intentional malicious patterns, not accidental security debt.

## Related

- [A Systematization of Security Vulnerabilities in Computer Use Agents](https://arxiv.org/pdf/2507.05445) — Broader agent security taxonomy
- [A Survey on Agentic Security: Applications, Threats and Defenses](https://www.arxiv.org/pdf/2510.06445) — Comprehensive defense strategies
- [Agentic AI Security: Threats, Defenses, Evaluation, and Open Challenges](https://arxiv.org/html/2510.23883v1) — Evaluation frameworks for agentic systems
