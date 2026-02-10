# Claude Code for Infrastructure

| | |
|---|---|
| **Source** | Fluid.sh |
| **URL** | [fluid.sh](https://www.fluid.sh/) |
| **Researched** | 2026-02-05 |

## Overview

Fluid.sh is a sandbox-first terminal agent that solves a fundamental problem in AI-driven infrastructure automation: LLMs excel at generating Infrastructure-as-Code (Terraform, Ansible) but lack real-world context about existing production systems. By provisioning ephemeral clones of VMs and Kubernetes clusters, agents safely explore infrastructure state, test changes interactively, and auto-generate reproducible playbooks—with human approval gates before production deployment.

## Key Points

- **Sandbox-First Isolation**: Rather than granting SSH access to production, Fluid creates instant ephemeral VM/cluster clones where agents safely experiment without affecting live systems.
- **Evidence-Based Code Generation**: Agent discovers actual OS type, installed packages, and available CLI tools before writing deployment code—eliminating guesswork from IaC generation.
- **Human-in-the-Loop Safety**: Approval gates required for resource-constrained operations (sandbox creation on low-memory hosts) and privileged actions (package installation, internet access).
- **Audit Trail Integration**: Complete command logging and change tracking enable comprehensive review before any production impact.

## Technical Details

**Workflow Progression:**
1. Create ephemeral sandbox (5s typical)
2. Agent explores infrastructure characteristics
3. Test changes interactively with live output streaming
4. System auto-generates Ansible Playbooks from observed commands
5. Human approval before production deployment

**Safety Mechanisms:**
- Ephemeral SSH certificates (temporary credentials, sandbox-only)
- Restricted package management without authorization
- No direct internet access without approval
- Designed for laptop/workstation deployment (not cloud-native infrastructure management tool)

**Operational Model:**
Unlike MCP servers or Claude plugins, Fluid operates as a standalone terminal agent, similar to Claude Code itself. This isolation prevents accidental privilege escalation while maintaining agent autonomy within bounded sandboxes.

## Implications

**For Infrastructure Teams:**
This approach transforms IaC from a brittle "write once, hope it works" model to an exploratory, evidence-driven process. Agents gather actual system context before generating code, reducing template drift and configuration entropy.

**Trade-offs:**
- Requires access to VM/container cloning infrastructure (cloud providers or on-prem virtualization)
- Adds latency to deployment (sandbox creation + exploration time) versus direct SSH
- Shifts automation bottleneck from "will this code work?" to "is this sandbox representative of production?"

**When to Use:**
Best suited for teams with heterogeneous infrastructure, legacy systems, or high compliance requirements where audit trails matter. Less valuable for homogeneous, immutable infrastructure patterns where server templates already encode state.

## Sources

- [Fluid.sh](https://www.fluid.sh/) - Sandbox-first terminal agent for infrastructure automation with Claude integration
