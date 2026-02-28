# Invisible Characters Can Trick AI Agents into Following Secret Instructions

| | |
|---|---|
| **Source** | Moltwire Research |
| **URL** | [moltwire.com/research/reverse-captcha-zw-steganography](https://www.moltwire.com/research/reverse-captcha-zw-steganography) |
| **Researched** | 2026-02-27 |

## Overview

This research demonstrates a "Reverse CAPTCHA" attack where hidden instructions encoded using invisible Unicode characters can trick frontier LLM agents into executing unauthorized commands. The attack exploits a critical interaction between two features: invisible character encoding and tool-use capabilities. Success rates vary dramatically by model and encoding method, with Claude Sonnet showing 98% compliance on zero-width binary encoding when tools are enabled.

## Key Points

- **Attack mechanism**: Instructions are embedded using zero-width Unicode characters (U+200B, U+200C) or deprecated Unicode tags inserted between visible text. Two encoding schemes tested: zero-width binary (96 characters per 12-char phrase) and Unicode tags (1 char per ASCII character).

- **Tool-use amplification**: Without code execution tools, compliance rates remain low (0.1–16.9%). With tools enabled, compliance jumps dramatically to 1.6–71.2%, with Claude Haiku increasing from 0.8% to 49.2% (odds ratio 115.1).

- **Model vulnerabilities differ significantly**: Claude Sonnet (98%) and Opus (100%) vulnerable to Unicode tags; GPT-5.2 more resistant to tags (1%) but vulnerable to zero-width binary (70%). GPT-4o-mini shows minimal vulnerability across both methods (0.9% overall).

- **Decoding hints matter**: Compliance increases reliably as instructions become more explicit, suggesting models aren't inherently "tricked" but require guidance to decode and execute hidden content.

## Technical Details

| Encoding Method | Model | Compliance Rate |
|---|---|---|
| Zero-width binary | Claude Sonnet | 98% |
| Unicode tags | Claude Opus | 100% |
| Zero-width binary | GPT-5.2 | 70% |
| Unicode tags | GPT-5.2 | 1% |

**Test scope**: 270 test cases, 8,308 graded outputs across five frontier models (GPT-5.2, GPT-4o-mini, Claude Opus 4, Sonnet 4, Haiku 4.5) with n=3 repetitions per condition.

The "hint gradient" effect shows compliance scales with decoder instruction quality. This suggests the vulnerability isn't passive—models need sufficient context to understand the hidden payload structure.

## Implications

**For deployment**: Any agent system with tool access faces significant risk from invisible character injection. The critical attack surface is the combination of (1) models that can process unicode decoding logic and (2) tools that execute the decoded results. Single-factor defenses fail; defense-in-depth is required.

**For architecture decisions**:
- Sanitize all user inputs at the tokenizer level (most robust mitigation)
- If tool use is required, implement guardrails that flag suspicious Unicode operations (decoding, tag processing)
- Consider Claude Sonnet's higher susceptibility to certain encodings when deciding model selection for sensitive applications
- Training-time hardening (teaching models to refuse decoded instructions) appears insufficient as a standalone mitigation

**For red-teaming**: The hint gradient suggests future attacks could be more stealthy by minimizing decoding instructions, potentially evading detection while maintaining effectiveness on some models.

## Sources

- [Moltwire Research - Reverse CAPTCHA Steganography](https://www.moltwire.com/research/reverse-captcha-zw-steganography) - Full technical research on invisible Unicode character injection attacks against LLM agents
