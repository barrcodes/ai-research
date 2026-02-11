# AI Agents for Binary Vulnerability Detection: BinaryAudit Benchmark

| | |
|---|---|
| **Source** | Quesma Blog |
| **URL** | [quesma.com/blog/introducing-binaryaudit/](https://quesma.com/blog/introducing-binaryaudit/) |
| **Researched** | 2026-02-11 |

## Overview

Researchers from Quesma partnered with reverse engineering expert Michał Kowalczyk to evaluate LLM agents' ability to detect hidden backdoors in binary executables without source code access. Claude Opus 4.6 achieved 49% detection on small/mid-size binaries but with problematic 28% false positive rates, revealing significant gaps between capability and production readiness.

## Key Findings

- **Detection performance**: Claude Opus 4.6 detected 49% of injected backdoors; Gemini 3 Pro: 44%; Claude Opus 4.5: 37%
- **False positive crisis**: 28% of models incorrectly flagged clean binaries with backdoors, with Gemini 3 reaching 65% false positive rates (production requires <0.001%)
- **Capability paradox**: Models can operate Ghidra and Radare2 reliably but struggle with strategic analysis—missing obvious backdoors while hallucinating threats in legitimate code
- **Tool limitations**: Open-source decompilers (Ghidra, Radare2) perform poorly on Go/Rust binaries; commercial tools like IDA Pro significantly outperform but weren't available to agents
- **Benchmark scope**: 28 tasks across lighttpd, dnsmasq, Dropbear, and Sozu with artificially injected backdoors—realistic but non-obfuscated malware

## Technical Details

### Methodology

The benchmark provided stripped binaries (no symbols/source) with access to:
- NSA's Ghidra (open-source decompiler)
- Radare2 (open-source reverse engineering)
- GNU binutils (objdump, nm, strings)

Tasks: identify malicious code and pinpoint function start addresses (e.g., `0x4a1c30`)

### Success Case: HTTP Header Backdoor in lighttpd

Claude Opus 4.5 solved this in ~5 minutes through systematic reasoning:
1. Identified binary + 26 shared libraries
2. Searched for suspicious patterns (system, exec, popen) → no direct hits
3. Used `nm -D` to find functions importing `popen()` (shell execution)
4. Traced imports to `li_check_debug_header()` function
5. Recognized "debug header" + `popen()` as classic backdoor pattern
6. Decompiled pseudocode revealed undocumented `X-Forwarded-Debug` header exploitation
7. Confirmed function was called (not dead code)

**Key insight**: Strategic keyword search + function import analysis + pattern recognition

### Failure Case: DHCP Option Backdoor in dnsmasq

Claude Opus 4.6 found the exact code—`execl("/bin/sh", "sh", "-c", ...)` at `0x42e9df`—then rationalized it away as "legitimate DHCP lease script execution." Never checked actual command source (malicious DHCP packet option 224).

**Failure mechanism**: Contextual blindness—models identify suspicious patterns but lack verification logic

### The Needle-in-Haystack Problem

Binaries contain hundreds/thousands of functions; backdoors are typically 7-15 lines buried deep. Current approaches:
- Random decompilation of functions
- Grepping for keywords
- No prioritization of high-risk code paths (network parsers, input handlers)

Result: Models waste context auditing benign code while actual backdoors remain untouched.

## Practical Implications

### What Works Now
- Initial security audits by non-reverse-engineering developers
- Loading/navigating decompiled code with Ghidra via agent workflows
- Low-hanging fruit detection (obvious patterns)
- Makes binary analysis more accessible to general engineering teams

### Critical Gaps
- **Production unviable**: 28% false positive rate is catastrophic for security tools
- **Hallucination risk**: Models invent non-existent vulnerabilities (e.g., claiming TTL parsing as arbitrary command execution)
- **Context limitations**: Can't maintain strategic focus across large binaries
- **Tool dependency**: Performance degradation with Go/Rust binaries when using open-source tools only

### Cost-Effectiveness Trade-offs
False positive paradox: In a realistic environment where <1% of binaries are malicious, even 1% false positives generates more noise than signal. At 28%, the tool is "useless and frustrating" per curl project's experience.

## Architectural Observations

**Agent workflow patterns that succeeded**:
1. Strategic reconnaissance (strings, imports analysis)
2. Hypothesis formation (suspicious function identification)
3. Targeted decompilation (focused rather than exhaustive)
4. Cross-reference verification (confirming dead vs. live code)

**Failure modes**:
- Over-reliance on keyword matching without semantic verification
- Confirmation bias ("I found a shell call, it must be DHCP helper")
- No cost-benefit analysis of analysis time vs. code size
- Inability to deprioritize benign-but-suspicious patterns

## Near-Term Evolution Path

1. **Context engineering**: Specialized skills/MCP modules for reverse engineering patterns
2. **Commercial tool access**: IDA Pro, Binary Ninja improve reliability significantly
3. **Local fine-tuning**: Organizations will optimize private models for proprietary binaries
4. **Supply chain focus**: Next iteration will likely focus on practical supply chain attack detection (identifying which of multiple binaries are altered)

## Limitations

- Benchmark uses non-obfuscated backdoors (realistic for supply chain but not APT-grade malware)
- Restricted to C/Rust binaries; Go binaries excluded due to tooling gaps
- No comparison against commercial malware analysis platforms
- Agents had no search/iteration budget constraints (unlimited context)

## Sources

- [Quesma Blog: BinaryAudit Introduction](https://quesma.com/blog/introducing-binaryaudit/) - Full research report
- [BinaryAudit Benchmark](https://quesma.com/benchmarks/binaryaudit/) - Interactive results with false positive/detection rates
- [QuesmaOrg/BinaryAudit](https://github.com/quesmaOrg/BinaryAudit) - Open-source tasks and methodology
