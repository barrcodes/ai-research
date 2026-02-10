# open-guard v0.1.0: Defense-in-Depth Security for AI Coding Assistants

| | |
|---|---|
| **Source** | Reddit r/ClaudeAI |
| **URL** | [old.reddit.com/r/ClaudeAI/comments/1qtse1t/openguard_v010/](https://old.reddit.com/r/ClaudeAI/comments/1qtse1t/openguard_v010_defenseindepth_security_for_ai/) |
| **Researched** | 2026-02-02 |

## Overview

open-guard is an open-source security layer designed to protect codebases from prompt injection, malicious commands, and harmful content when using AI coding assistants. The project implements a four-layer defense-in-depth architecture that combines obfuscation detection, regex pattern matching, agent-based analysis, and LLM safety filtering. Early results show 75-100% threat detection with zero false positives on safe prompts, with the agent analysis layer catching 94% of novel attacks that bypass regex patterns entirely.

## Key Points

- **Four-Layer Architecture**: Combines decoding obfuscation (Base64, Hex, ROT13, Unicode), 93 regex patterns for known threats, agent-based analysis for novel attacks, and llama-guard3 for unsafe content classification
- **High Accuracy**: Achieves 75-100% threat detection rate with zero false positives on benign inputs; agent layer specifically catches 94% of novel attacks escaping regex
- **Sandbox Execution**: Analyzer runs in isolated sandbox with read-only tools, temp directory execution, user settings only, and MCP servers disabled via `--strict-mcp-config`
- **Multi-Platform Support**: Released for Linux (amd64/arm64), macOS (amd64/arm64), and Windows (amd64)
- **Language & Stack**: Written in Go, powered by open-source Claude Agent SDK
- **Planned Integration**: Roadmap includes Claude Code plugin with PreToolUse hook for automatic prompt and tool input scanning

## Technical Details

### Detection Architecture

The system implements layered threat detection:

```
Input → Decode Obfuscation → Pattern Matching → Agent Analysis → LLM Safety → Allow/Block
         (Layer 0)           (Layer 1)         (Layer 2)        (Layer 3)
```

**Layer 0 - Obfuscation Decoding**: Handles Base64, Hex, ROT13, and Unicode encoding to surface hidden payload content before pattern analysis.

**Layer 1 - Pattern Matching**: 93 regex patterns target known threat signatures (T1-T9 threat categories) with immediate blocking on match.

**Layer 2 - Agent Analysis**: Uses Claude/Ollama as a learned threat analyzer. This layer is critical for catching novel attacks—the 94% novel attack detection rate shows the value of LLM-based semantic analysis over purely syntactic pattern matching. The agent evaluates injection risk for patterns that bypass regex.

**Layer 3 - LLM Safety**: llama-guard3 model applies safety classification (S1-S13 safety categories) to catch unsafe content regardless of injection technique.

### Security Hardening

The analyzer runs with strict isolation:
- Temp directory execution prevents filesystem modification
- Read-only tooling restricts capability surface
- User settings only—system configuration access denied
- MCP servers disabled during analysis via `--strict-mcp-config` flag

This sandboxing prevents the analyzer itself from becoming an attack surface.

### Detection Rates & Metrics

- **Safe Prompt Accuracy**: Zero false positives on benign inputs
- **Threat Detection**: 75-100% range depending on attack complexity
- **Novel Attack Coverage**: 94% detection rate on attacks designed to bypass regex patterns
- **Performance**: Designed for minimal overhead in CI/CD pipelines and interactive workflows

## Implications

### For Platform Architects

The four-layer approach represents a pragmatic defense-in-depth strategy for AI-assisted development environments. The key architectural insight is layering fast (regex), specialized (agent), and general (LLM safety) detection. This reduces both false positives and false negatives compared to single-layer approaches.

The sandbox isolation model for the analyzer itself should be considered a requirement—security tools can become attack vectors if they have unconstrained access. The `--strict-mcp-config` disabling and temp-directory execution pattern is worth adopting elsewhere.

### For Development Teams Using Claude

The 94% novel attack detection via agent analysis demonstrates that LLM-based threat analysis isn't just marketing—it materially improves detection of attacks specifically designed to evade pattern matching. This is relevant for teams running Claude with extended tool access or in higher-trust internal environments.

The zero false positives metric is critical: many security tools create friction through false alarms. Zero false positives on safe prompts means teams can integrate this into pre-commit hooks or tool use pipelines without productivity impact.

### For Security Tool Design

The progression from regex → agent analysis → safety classification mirrors a general pattern in security: static analysis catches known patterns, semantic analysis catches novel variants, and classifiers catch boundary cases. This tiered approach is applicable beyond prompt injection.

The fact that regex is Layer 1 (not discarded entirely) while agent analysis is Layer 2 (specialized) suggests a maturity in the project's threat modeling. Fast path through regex, escalate only ambiguous cases to costlier agent analysis.

### For Claude Code Integration

The planned PreToolUse hook integration means prompt injection defense can happen transparently before tool execution—a key architectural difference from post-hoc logging or alerts. If integrated as a prerequisite before any tool invocation, it becomes a structural control rather than a monitoring tool.

## Technical Ecosystem

**Related Projects by Author (severity1):**
- [claude-code-prompt-improver](https://github.com/severity1/claude-code-prompt-improver) (1.1k stars) - Intelligent prompt improvement hook
- [claude-code-auto-memory](https://github.com/severity1/claude-code-auto-memory) (96 stars) - Auto-maintains CLAUDE.md configuration
- [claude-agent-sdk-go](https://github.com/severity1/claude-agent-sdk-go) (77 stars) - Go SDK underlying open-guard
- [custom-amazon-bedrock-agent-action](https://github.com/severity1/custom-amazon-bedrock-agent-action) (38 stars) - GitHub Action for Bedrock Agent PR reviews

**Competitive Landscape:**
- ContextGuard (separate project) targets MCP server security with similar layered detection
- SecureShell (divagr18) focuses on shell command execution safety for LLM agents
- These projects address complementary concerns in the AI coding assistant supply chain

## Sources

- [open-guard v0.1.0 Reddit post](https://old.reddit.com/r/ClaudeAI/comments/1qtse1t/openguard_v010_defenseindepth_security_for_ai/) - Original announcement with full technical details
- [severity1 GitHub profile](https://github.com/severity1) - Author's other security-focused projects for Claude Code
- [Claude Agent SDK Go](https://github.com/severity1/claude-agent-sdk-go) - Underlying framework powering detection agent
