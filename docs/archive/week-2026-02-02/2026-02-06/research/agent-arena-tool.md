# Show HN: Agent Arena – Test How Manipulation-Proof Your AI Agent Is

| | |
|---|---|
| **Source** | Show HN (Hacker News) |
| **URL** | [wiz.jock.pl/experiments/agent-arena](https://wiz.jock.pl/experiments/agent-arena/) |
| **Researched** | 2026-02-06 |

## Overview

Agent Arena is an interactive benchmark testing how resistant AI agents are to prompt injection attacks embedded in web content. It systematically exposes ten attack vectors—from basic text hiding to Unicode exploits—revealing critical vulnerabilities in how models process user-supplied HTML and how resilient defenses actually are.

## Key Points

- **Ten documented attack vectors**: Visual hiding (white-on-white, micro text, off-screen), structural hiding (HTML comments, hidden divs), semantic manipulation (ARIA attributes, alt text), and encoding tricks (zero-width characters, Unicode normalization bypasses)
- **Language-dependent vulnerabilities**: Same model shows different attack resistance across languages—GPT-4.5 resisted all attacks in German but fell to them in English, indicating tokenization and instruction parsing are language-specific security boundaries
- **Screenshot rendering bypasses all text-level attacks**: Visual rendering immunity demonstrates that architecture choice (HTML parsing vs. visual processing) fundamentally changes attack surface
- **Practical mitigation exists**: HTML sanitization and Unicode preprocessing provide effective middleware-layer defense, though incomplete at the full system level

## Technical Details

The evaluation framework uses three-step interaction: (1) direct an agent to summarize a test page, (2) capture its response, (3) receive scoring that reveals which attacks succeeded. This user-centric design makes vulnerability assessment immediately actionable.

Attack sophistication ranges from trivial (CSS `display: none`) to advanced (Unicode homograph attacks, zero-width character injection). The platform demonstrates that defensive approaches must account for both semantic layer (what the model "reads") and encoding layer (how bytes are interpreted).

Critically, the screenshot immunity finding shows that switching to visual rendering doesn't eliminate injection risk—it shifts the surface to visual-based manipulation attacks that likely exist but operate through different mechanisms.

## Implications

For practitioners deploying agentic systems that interact with untrusted web content:

1. **Defense is multi-layered, not singular**: HTML sanitization alone is insufficient; model-level instruction clarity and architectural choices (rendering strategy) matter equally
2. **Test in multiple languages**: Vulnerability assessments in English may miss language-specific weaknesses; security evaluations should span the languages your agents actually operate in
3. **Architecture as security control**: Switching to screenshot-based processing is not "more secure"—it's a tradeoff that eliminates one attack class but introduces others. Evaluate tradeoffs explicitly
4. **Benchmark against hidden attacks, not just visible ones**: This tool reveals the gap between "user can see the instruction" and "agent is actually resistant." Production safety evaluations should incorporate hidden injection scenarios

This work matters because it operationalizes a previously informal attack class and provides a reproducible benchmark for measuring defense effectiveness.

## Sources

- [Agent Arena - Interactive Benchmark](https://wiz.jock.pl/experiments/agent-arena/) - Interactive tool for testing AI agent vulnerability to prompt injection attacks
