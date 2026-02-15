# Attackers Prompted Gemini Over 100,000 Times While Trying to Clone It

| | |
|---|---|
| **Source** | Ars Technica / Google Threat Intelligence |
| **URL** | [arstechnica.com/ai/2026/02/attackers-prompted-gemini-over-100000-times-while-trying-to-clone-it-google-says](https://arstechnica.com/ai/2026/02/attackers-prompted-gemini-over-100000-times-while-trying-to-clone-it-google-says/) |
| **Researched** | 2026-02-15 |

## Overview

Google disclosed that "commercially motivated" actors conducted sustained model distillation attacks against Gemini, with one campaign executing over 100,000 prompts to extract the model's reasoning logic and decision-making patterns. The attackers—believed to be private companies or researchers seeking competitive advantage—represent a new category of IP theft targeting deployed LLMs, with Google's Threat Intelligence chief warning this represents the "canary in the coal mine" for industry-wide vulnerability.

## Key Points

- **Attack Mechanism**: Distillation attacks use systematic prompting to reverse-engineer model internals—specifically targeting reasoning algorithms that determine how the model processes information.

- **Scale**: A single campaign exceeded 100,000 prompts, with multiple concurrent campaigns detected. The volume suggests automated extraction infrastructure rather than ad-hoc probing.

- **Attacker Profile**: Commercially motivated (not state actors or criminal groups). Likely include competitors building rival models or companies bolstering existing AI systems.

- **Detection**: Google identified anomalous prompting patterns and traced repeated extraction attempts back to specific sources, enabling targeted blocking.

- **Industry Signal**: John Hultquist (Google Threat Intelligence) framed Gemini attacks as predictive—smaller companies' custom AI tools now face identical vulnerability patterns. OpenAI previously accused DeepSeek of comparable distillation efforts.

## Technical Details

Google's defenses include:
- Pattern monitoring to detect extraction-specific query sequences
- Source-level blocking of identified attackers
- Rate limiting and behavioral analysis

The fundamental vulnerability is architectural: **major LLMs remain openly accessible via internet APIs**, making sustained defense difficult despite detection capabilities. Attackers can explore the model's output space at scale—different prompts reveal different behaviors that, when aggregated, approximate the underlying decision logic.

The attack targets reasoning specifically—the sequential logic chains that make models useful. This is harder to defend than output filtering because reasoning is the model's core capability, not a detachable component.

## Implications

**For Architecture & Security Teams:**

1. **Open API Exposure**: Deployed models with public access are inherently vulnerable to extraction. No detection-only approach prevents this—blocking requires either rate limiting hard enough to break legitimate use, or accepting some extraction risk.

2. **IP Risk Quantification**: If your model represents significant competitive advantage, distillation attacks transform it into shared IP across competitors within months. This changes valuation and retention economics for proprietary models.

3. **Defense Trade-offs**:
   - Rate limiting reduces attack surface but degrades user experience
   - Behavioral blocking works until attackers distribute across botnets
   - Output perturbation (adding noise) degrades model quality
   - Fine-grained API control (quota-per-user) adds operational complexity

4. **Emerging Pattern**: Distillation attacks will become standard reconnaissance for building competing systems. Budget assumes your deployed model will be partially reconstructed by competitors.

5. **Model Release Decisions**: Organizations must now factor extraction risk when deciding between proprietary vs. open-source releases. The attack surface area matters—a model serving 10M users daily is a higher-value target.

**Actionable Mitigation:**
- Implement graduated rate limiting (strict limits on extraction-like patterns)
- Monitor query clustering (similar prompts across short timeframes)
- Consider knowledge cutoff on newer proprietary techniques before deployment
- Assume competitive extraction and plan accordingly in model release strategy

## Sources

- [Google says attackers used 100,000+ prompts to try to clone AI chatbot Gemini](https://www.nbcnews.com/tech/security/google-gemini-hit-100000-prompts-cloning-attempt-rcna258657) - NBC News reporting on the attack campaign
- [Over 100,000 Prompts Used In Attempt To Steal Gemini's Reasoning Logic](https://dataconomy.com/2026/02/12/over-100000-prompts-used-in-attempt-to-steal-gemini-reasoning-logic/) - Technical breakdown of distillation methodology
- [Google says attackers hit Gemini with 100,000 prompts, but what they were really after is bigger than spam](https://attackofthefanboy.com/tech/google-says-attackers-hit-gemini-with-100000-prompts-but-what-they-were-really-after-is-bigger-than-spam/) - Analysis of IP theft implications
