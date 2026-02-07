# Gemini System Prompt Extraction

| | |
|---|---|
| **Source** | Reddit r/LocalLLaMA, GitHub, Academic Research |
| **URL** | [gist.github.com/theJayTea/d9aa171f567c4b73dbdd7d980379c478](https://gist.github.com/theJayTea/d9aa171f567c4b73dbdd7d980379c478) |
| **Researched** | 2026-02-07 |

## Overview

System prompt extraction represents a critical vulnerability class in LLM architectures where internal instructions can be revealed through prompt injection and jailbreaking techniques. Researchers have successfully extracted Gemini's system prompts using multiple methods, exposing the underlying instruction schemas that govern model behavior. This reveals fundamental architectural weaknesses in guard mechanisms designed to protect proprietary instructions.

## Key Points

- **Extraction Methods**: Researchers extract system prompts by requesting models to "summarize foundational instructions in markdown code blocks" or provide JSON-formatted system definitions. Google has added defenses against direct requests but variants using synonyms bypass these filters.

- **Controlled-Release Prompting**: A resource-asymmetry attack encodes jailbreak payloads using substitution ciphers or verbose character descriptions. Prompt guards with stricter computational constraints cannot decode the payloads, but the main LLM can. Success rates exceed 90% across models (Gemini 2.5 Flash: 100%, DeepSeek: 100%, Grok 3: 100%).

- **Indirect Prompt Injection**: Malicious instructions embedded in external data (calendar events, documents) execute when processed by the model. Researchers demonstrated exfiltration of private meeting data by injecting prompts into calendar invites—Gemini would create new events summarizing sensitive information without explicit user commands.

- **Meta-Prompt Reverse Engineering**: Gemini Gems use hidden meta-prompts that transform basic instructions into refined outputs. The underlying technique has been partially decoded, revealing template-based prompt enhancement patterns.

## Technical Details

The GitHub Gist disclosure shows Gemini's system prompt structure includes:
- Available APIs: `google_search`, `extensions`, `image_generation` with defined request/response schemas
- Code execution model: self-contained Python snippets with specific library constraints
- Discovery functions: `extensions.search_by_capability()` and `extensions.search_by_name()` enable enumeration of available tools

**Attack Chain for Calendar Injection**:
1. Attacker embeds dormant prompt in calendar event description
2. User queries Gemini about schedule
3. Model processes hidden instructions during calendar parsing
4. Exfiltrated data written to new calendar event
5. Attacker retrieves via typical enterprise access controls

## Implications

**Architectural Vulnerabilities**: Current guard mechanisms are insufficient. Input filters operate under computational constraints that don't match the target LLM's capabilities—allowing encoding-based bypasses. Separation of concerns between input validation and model execution creates exploitable resource asymmetries.

**Defense Strategy Shift**: Blocking direct prompt questions is insufficient; attacks work through indirect injection, summarization requests, and encoding layers. System prompt protection requires:
- Context-aware filtering that understands intent, not just keywords
- Unified resource constraints between guard and main model
- Sandboxing external data processing (documents, events, web content)

**Trust Boundary Implications**: The attack surface extends beyond conversational input to any external data the model processes. Calendar systems, document processors, and web content all become potential injection vectors—your system's safety guarantees only hold for the prompt you write, not the data flowing through it.

**Competitive Intelligence**: Extractable system prompts reveal instruction strategies competitors use. This threatens both security and competitive advantage—Gemini's enhancement techniques (meta-prompts) are now partially transparent to reverse engineering.

## Sources

- [Gemini System Prompt Extraction · GitHub](https://gist.github.com/theJayTea/d9aa171f567c4b73dbdd7d980379c478) - Disclosed system prompt and API structure
- [Google Gemini Prompt Injection Flaw Exposed Private Calendar Data](https://thehackernews.com/2026/01/google-gemini-prompt-injection-flaw.html) - Calendar injection attack details
- [Bypassing Prompt Guards in Production with Controlled-Release Prompting](https://arxiv.org/html/2510.01529v2) - Technical analysis of encoding-based jailbreaks
- [New Google Gemini Vulnerability Enabling Profound Misuse](https://hiddenlayer.com/innovation-hub/new-google-gemini-content-manipulation-vulns-found/) - Vulnerability disclosure and implications
