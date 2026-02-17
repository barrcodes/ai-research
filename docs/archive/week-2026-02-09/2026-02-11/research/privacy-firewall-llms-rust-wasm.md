# Sunder: Privacy Firewall for LLMs Using Rust + WASM

| | |
|---|---|
| **Source** | r/LocalLLaMA |
| **URL** | [old.reddit.com/r/LocalLLaMA/comments/1r1zj0b/](https://old.reddit.com/r/LocalLLaMA/comments/1r1zj0b/showcase_i_built_a_browserbased_privacy_firewall/) |
| **Researched** | 2026-02-11 |

## Overview

Sunder is a Chrome extension that implements a zero-trust privacy firewall between users and cloud-based LLM providers. Built by AWX-Houcine, it addresses the fundamental concern that even with privacy settings enabled, sensitive data (emails, transaction IDs, client names) may be stored on provider servers. The extension intercepts user input at the browser level, strips sensitive information before transmission, and locally restores redacted tokens in LLM responses.

## Key Points

- **Zero-trust architecture**: Assumes all LLM providers will retain input data; prevents PII transmission by design rather than relying on provider policies
- **Multi-provider coverage**: Works with ChatGPT, Claude, Gemini, Perplexity, DeepSeek, Copilot, and local Ollama instances
- **Fully local operation**: 100% browser-based processing via WASM; no remote calls to sanitization services
- **In-memory identity vault**: Maintains token-to-value mappings exclusively in browser memory; never persisted to disk or transmitted to servers
- **Pattern-based protection**: Regex-based detection for emails, dates, monetary amounts, credit card numbers with token substitution
- **Context preservation**: AI receives full semantic context through sanitized tokens rather than actual values
- **Bidirectional redaction**: LLM responses are automatically de-tokenized client-side before display

## Technical Details

### Architecture

**Core Components:**
1. **Rust → WASM engine**: Performance-critical sanitization logic compiled from Rust to WebAssembly for in-browser execution
2. **Plasmo framework**: React-based Chrome extension wrapper providing UI and lifecycle management
3. **Content script intercepts**: Catches user input before network transmission
4. **Identity vault**: In-memory hash map mapping tokens (e.g., `[EMAIL_1]`) to original values with automatic cleanup

**Data Flow:**
- User input → Content script intercepts → Rust/WASM pattern matcher → Token substitution → Network transmission
- LLM response → Received in browser → Token lookup in vault → Value restoration → Display to user

### Implementation Strategy

**Regex-based pattern matching** handles common PII categories:
- Email: `john.doe@gmail.com` → `[EMAIL_1]`
- Currency: `$50,000` → `[MONEY_1]`
- Payment cards: `4242 4242 4242 4242` → `[CARD_1]`
- Dates and temporal data

**Challenges acknowledged by author:**
- Named Entity Recognition (NER) in WASM for contextual name detection without bloating binary size
- Trade-off between coverage and extension size
- Performance optimization for real-time interception without user-perceptible latency

### Browser Integration

- Works transparently with existing LLM UIs (no UI changes required from user perspective)
- Content script attachment to chat interfaces
- Service worker for background state management
- Local dashboard for Ollama integration enabling air-gapped workflows

## Implications

### For Practitioners

1. **Privacy architecture shift**: Moves from trusting provider privacy policies to enforcing guarantees at client layer. This pattern applies broadly to any sensitive data processing in cloud tools.

2. **Performance-critical WASM**: Demonstrates practical use of Rust-compiled WASM for latency-sensitive browser operations. Binary size becomes a real constraint (NER bloat concern).

3. **Token-based redaction pattern**: Applicable beyond LLMs—any scenario where you need to preserve semantic context while removing direct PII references (logging, audit trails, data pipelines).

4. **Ollama integration value**: Local model + local privacy layer enables compliance-grade deployments without API dependencies or third-party data flow.

5. **Extension ecosystem maturity**: Production Chrome extensions increasingly handle security-critical operations (this is trust-critical code). Deployment model bypasses traditional software distribution channels.

### Architectural Trade-offs

| Aspect | Trade-off |
|--------|-----------|
| **Coverage** | Regex catches structured PII well; contextual names require expensive NER |
| **Size** | WASM binary must remain small for extension distribution; feature-complete NER may exceed practical limits |
| **Context loss** | Tokenization preserves semantic meaning but users lose audit visibility into what was redacted |
| **Latency** | Real-time pattern matching on every keystroke; WASM helps but CPU overhead on large payloads |
| **Scope** | Browser-only; doesn't protect against endpoint compromise or browser-level exfiltration |

### Open Problems

1. **Efficient NER in WASM**: Author explicitly seeking solutions for catching contextual names/entities without excessive binary bloat
2. **Response validation**: How to verify LLM actually used sanitized tokens vs. reconstructing from context?
3. **Metadata leakage**: Message length, structure, timing patterns still visible to LLM provider
4. **Extension trustworthiness**: Users must trust Sunder code doesn't exfiltrate to its own backend (inspection required)

## Sources

- [Sunder Privacy Firewall Reddit Post](https://old.reddit.com/r/LocalLLaMA/comments/1r1zj0b/showcase_i_built_a_browserbased_privacy_firewall/) - Original showcase post on r/LocalLLaMA with technical details and author discussion
- [GitHub Repository](https://github.com/awixor/sunder-ai) - Source code and contributions
- [Live Dashboard](https://sunder-ai-dashboard.vercel.app) - Ollama integration demo
