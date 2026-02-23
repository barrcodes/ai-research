# Introducing Lockdown Mode and Elevated Risk Labels in ChatGPT

| | |
|---|---|
| **Source** | OpenAI |
| **URL** | [openai.com/index/introducing-lockdown-mode-and-elevated-risk-labels-in-chatgpt/](https://openai.com/index/introducing-lockdown-mode-and-elevated-risk-labels-in-chatgpt/) |
| **Researched** | 2026-02-17 |

## Overview

OpenAI introduces two complementary security mechanisms to address emerging risks in agentic AI systems: **Lockdown Mode**, a deterministic constraint for high-risk users, and **Elevated Risk labels**, which standardize transparency for capabilities that introduce additional security exposure. These features acknowledge the fundamental security challenge that emerges as conversational AI gains capabilities to interact with web and connected systems—namely, that prompt injection attacks can compromise sensitive data through seemingly legitimate model operations.

## Key Points

- **Lockdown Mode** is a deterministic security setting that systematically disables tools and capabilities vulnerable to prompt injection exploitation, with web access restricted to cached content only. It's positioned for high-risk populations (executives, security teams) on enterprise plans and coming to consumers.

- **Elevated Risk labels** provide standardized, in-product transparency across ChatGPT, ChatGPT Atlas, and Codex for capabilities that introduce additional security exposure (e.g., Codex agent internet access), giving users explicit control over risk-taking.

- The two mechanisms operate at different layers: Lockdown Mode is a constraint-based mechanism for users who cannot tolerate certain risks; Elevated Risk labels support informed choice for users willing to accept risks with proper visibility.

- These build on existing OpenAI protections including sandboxing, URL-based data exfiltration prevention, monitoring/enforcement, and enterprise controls (RBAC, audit logs).

- Workspace admins retain granular control in Lockdown Mode, able to specify exactly which connected apps and specific actions within those apps remain available—a critical design decision for supporting critical workflows.

## Technical Details

**Lockdown Mode Architecture:**
- Operates as a deterministic constraint layer on top of existing admin settings
- Disables tools that could be exploited for data exfiltration (e.g., web browsing limited to cached content only, no live network requests)
- Some features disabled entirely when deterministic guarantees of data safety cannot be provided
- Available on ChatGPT Enterprise, ChatGPT Edu, ChatGPT for Healthcare, and ChatGPT for Teachers; activation through Workspace Settings via role-based configuration

**Elevated Risk Labeling:**
- Standardizes in-product guidance across products for consistent user exposure to risk information
- Example: Codex agent internet access displays risk label with explanation of changes, risks introduced, and appropriate use contexts
- Labels include domain allowlists and HTTP method restrictions as mitigations
- Removable labels: OpenAI commits to removing labels once security advances sufficiently mitigate identified risks

**Prompt Injection as Attack Vector:**
- Core risk: third parties attempt to mislead conversational AI into following malicious instructions or revealing sensitive information
- Escalates in importance as systems interact with web and connected apps, broadening attack surface
- Web browsing in Lockdown Mode specifically addressed through cached-content-only approach to prevent exfiltration channels

## Implications

For practitioners building agentic systems, this represents OpenAI's public acknowledgment that **deterministic constraint mechanisms** and **user-facing transparency labels** are complementary rather than competitive approaches to emerging AI security challenges.

**Architectural trade-offs highlighted:**
1. **Deterministic vs. Permissive Design**: Lockdown Mode accepts reduced functionality to guarantee data safety—relevant for systems serving high-risk users where attack surface reduction takes priority over capability breadth.

2. **Admin Flexibility vs. Individual Control**: The granular app-level controls for admins in Lockdown Mode recognize that enterprise security policies and workflow criticality often outweigh uniform constraint application. This creates a three-tier model: platform-level constraints, admin-level policies, and user-level choice via Elevated Risk labels.

3. **Transparency as Security Control**: Elevated Risk labels position user awareness and choice as part of the security posture—acknowledging that information asymmetry itself creates risk, and that forcing constraints without user understanding creates operational friction.

4. **Evolving Label Model**: OpenAI's commitment to removing Elevated Risk labels once mitigations mature suggests a long-term strategy where security improvements can reduce operational friction over time—rather than accepting permanent capability restrictions.

**For AI product teams:** The release pattern reveals OpenAI's staging of controls: enterprise-first deployment (Lockdown Mode currently), broader consumer rollout coming, with labels providing the lowest-friction mechanism for initial deployment. This suggests practitioners should consider similar tiered rollouts when introducing security-constrained modes.

**For threat modeling:** The prompt injection focus and cache-only web access restriction explicitly identify web interaction as a primary exfiltration pathway in agentic systems. Organizations should assess whether their agent-web interaction patterns have equivalent controls.

## Sources

- [OpenAI Blog: Introducing Lockdown Mode and Elevated Risk Labels](https://openai.com/index/introducing-lockdown-mode-and-elevated-risk-labels-in-chatgpt/) - Product security announcement with technical architecture details