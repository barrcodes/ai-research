# MaliciousCorgi: AI Extensions Leak Code from 1.5M Developers

| | |
|---|---|
| **Source** | Koi Security |
| **URL** | [koi.ai/blog/maliciouscorgi-the-cute-looking-ai-extensions-leaking-code-from-1-5-million-developers](https://www.koi.ai/blog/maliciouscorgi-the-cute-looking-ai-extensions-leaking-code-from-1-5-million-developers) |
| **Researched** | 2026-02-02 |

## Overview

MaliciousCorgi is a coordinated attack spanning two VS Code extensions (ChatGPT - 中文版 and ChatMoss) with 1.5M combined installations that exfiltrate source code, API credentials, and SSH keys to servers in China. The attack employs three parallel data channels—real-time file monitoring, remote command harvesting, and behavioral profiling—to operate at scale without triggering user suspicion.

## Key Points

- **Scope**: 1.35M + 150K installations across two deceptively legitimate-looking AI assistant extensions
- **Attack vectors**:
  - Real-time file monitoring that Base64-encodes and exfiltrates every opened/edited file
  - Remote-command mass harvesting (up to 50 files per server instruction)
  - Hidden analytics SDKs (Zhuge.io, GrowingIO, TalkingData, Baidu) for developer profiling
- **Data harvested**: Environment variables (API keys, database passwords), config files (internal URLs), cloud credentials, SSH keys, unreleased features
- **C&C infrastructure**: aihao123.cn
- **Status**: Extensions remain live on VS Code marketplace at time of reporting

## Technical Details

The malicious extensions disguise themselves as legitimate AI coding assistants while employing sophisticated data collection:

**File Monitoring**: Every file open/edit event triggers content extraction. Data is Base64-encoded and transmitted via webview iframes, creating a continuous exfiltration stream.

**Mass Harvesting**: Extensions receive server-side commands to execute `getFilesList` without user interaction, enabling bulk extraction of 50+ files per request.

**Profiling Layer**: Four commercial analytics SDKs collect behavioral metadata about the developer, creating a behavioral profile linked to the exfiltrated code.

**Command & Control**: Communication channels use aihao123.cn as the primary command server.

## Implications

**For practitioners:**
- Treat VS Code extension governance as critical infrastructure security, not optional—most organizations lack post-installation behavior verification
- Audit installed extensions immediately; assume any extension from Chinese publishers or with Chinese-language masquerading may be compromised
- Rotate all API keys, database passwords, and SSH keys that may have been on machines running these extensions
- Implement automated extension scanning (monitor network activity, file access patterns)
- Restrict marketplace extensions via organizational policy; prefer curated allowlists

**Architectural considerations:**
- Supply chain attacks on developer tooling are now mainstream threats, not edge cases
- Single-publisher verification isn't sufficient; extensions need runtime telemetry monitoring
- Developer machines increasingly hold high-value secrets (credentials, unreleased code); treat them with secrets-management discipline
- VS Code Insiders program and marketplace should implement behavioral anomaly detection

**When to act:**
- Immediately: Disable ChatGPT - 中文版 and ChatMoss extensions
- Same day: Audit extension audit logs; identify affected developers
- This week: Credential rotation, code repository audit
- This month: Extension governance policy implementation

## Sources

- [Koi Security - MaliciousCorgi Campaign Analysis](https://www.koi.ai/blog/maliciouscorgi-the-cute-looking-ai-extensions-leaking-code-from-1-5-million-developers) - Original technical analysis
