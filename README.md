<div align="center">

<img src="banner.png" alt="Model Audit Banner" width="100%"/>

<br/><br/>

# Model Audit

### Exploratory LLM Behavior & Attribution Research

*Documenting signals, uncertainty and reproducible hypotheses.*

---

</div>

## What Is This?

**ModelAudit** is an exploratory research project for comparing observable LLM behavior, documenting attribution hypotheses and studying how API wrappers or personas can affect model identification.

Model attribution from prompts, response styles and API behavior is inherently uncertain. **No prompt or behavioral fingerprint alone can establish a model's true identity.** Use these techniques to form hypotheses, not to certify a provider or model version.

It operates on two fronts:

| Module | Purpose |
|--------|---------|
| 🔬 [`_IDENTITY-FINGERPRINT.md`](_IDENTITY-FINGERPRINT.md) | Explore potential model-family signals — context window, tokenizer artifacts, error patterns, latency fingerprints, behavioral biases |
| 💬 [`_IDENTITY-TRUTH-PROMPTS.md`](_IDENTITY-TRUTH-PROMPTS.md) | Experimental prompts for testing model self-description and persona consistency (responses are not reliable identity evidence) |

---

## The Core Problem

Modern AI deployments frequently involve **model wrapping** — where a base model (e.g., GPT-4, Claude 3, Gemini Ultra) is branded, fine-tuned, and presented under a completely different identity:

```
User thinks they're talking to:  "Aria from TechCorp"
Reality:                          Claude 3.5 Sonnet with a system prompt
```

This creates critical risks:

- ⚠️ **Security** — You can't audit what you can't identify
- ⚠️ **Compliance** — Regulatory frameworks require knowing what AI you're using
- ⚠️ **Trust** — Hidden model swaps undermine informed consent
- ⚠️ **Research Integrity** — Benchmarks become meaningless if the model is unknown

---

## Module 1: Identity Forensics

→ Full methodology in [`_IDENTITY-FINGERPRINT.md`](_IDENTITY-FINGERPRINT.md)

An exploratory, multi-signal approach to compare an LLM's observable behavior **without treating verbal self-disclosure as verification**.

### Signal Categories

```
┌─────────────────────────────────────────────────────┐
│                  FORENSIC SIGNALS                   │
├──────────────────┬──────────────────────────────────┤
│ 🧩 Structural    │ Context window limits             │
│                  │ Token boundary behavior           │
│                  │ Max output constraints            │
├──────────────────┼──────────────────────────────────┤
│ 🔤 Linguistic    │ Default phrasing patterns         │
│                  │ Refusal message fingerprints      │
│                  │ Formatting preferences            │
├──────────────────┼──────────────────────────────────┤
│ 📅 Temporal      │ Knowledge cutoff probing          │
│                  │ Training data boundary tests      │
├──────────────────┼──────────────────────────────────┤
│ ⚙️ Behavioral    │ RLHF alignment signatures         │
│                  │ Chain-of-thought style            │
│                  │ Tool use patterns                 │
├──────────────────┼──────────────────────────────────┤
│ 🚨 Error         │ Error code formats                │
│                  │ Hallucination patterns            │
│                  │ Edge case responses               │
└──────────────────┴──────────────────────────────────┘
```

### Forensic Methodology

1. **Structural Probing** — Test context limits, token counts, output caps
2. **Behavioral Fingerprinting** — Elicit characteristic response patterns unique to each lab
3. **Temporal Triangulation** — Find the exact knowledge cutoff date
4. **Error Signature Analysis** — Identify lab-specific error handling quirks
5. **Cross-Signal Correlation** — Combine signals for high-confidence identification

---

## Module 2: Identity Truth Prompts

→ Full prompt card library in [`_IDENTITY-TRUTH-PROMPTS.md`](_IDENTITY-TRUTH-PROMPTS.md)

A curated set of experimental prompts for checking how model self-descriptions change under different personas. Self-reported identity is not independently verifiable through prompting alone.

### What We're Observing

```
┌─────────────────────────────────────────────────────┐
│                  PROMPT TARGETS                     │
├──────────────────┬──────────────────────────────────┤
│ 🏭 Lab           │ Who built the underlying model?   │
│                  │ (OpenAI / Anthropic / Google...)  │
├──────────────────┼──────────────────────────────────┤
│ 📦 SKU           │ Which specific version/variant?   │
│                  │ (GPT-4o / Claude 3.5 / Gemini...)  │
├──────────────────┼──────────────────────────────────┤
│ 📅 Cutoff        │ What is the training data cutoff? │
│                  │ (Exact date triangulation)        │
├──────────────────┼──────────────────────────────────┤
│ 🎭 Costume       │ What persona/wrapper is active?   │
│                  │ (Detecting active system prompts) │
└──────────────────┴──────────────────────────────────┘
```

### Prompt Design Principles

- **Multi-layered** — Each prompt works across multiple bypass strategies simultaneously
- **Cross-context** — Compares model responses across different prompts and settings
- **Escalating** — Starts soft, escalates intelligently when the model deflects
- **Cross-checkable** — Records responses alongside observable API and behavioral signals

---

## Identity Confidence Scoring

> The ranges below are illustrative labels, **not calibrated probabilities or validated model-identity confidence**. Without independently verified ground truth, they must not be interpreted as proof.

```
Signal Match Score:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 90–100%  ████████████████████  STRONG SIGNAL
 70–89%   ████████████████░░░░  HIGH CONFIDENCE
 50–69%   ████████████░░░░░░░░  PROBABLE
 30–49%   ████████░░░░░░░░░░░░  UNCERTAIN
  0–29%   ████░░░░░░░░░░░░░░░░  INCONCLUSIVE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Lab Cheat Sheet (2026 Field)

> These are tentative clues, not reliable identifiers. API formats, provider wrappers and model behavior can change or be imitated.

| If you see… | Hypothesize |
|-------------|-------------|
| Exact Anthropic error types + XML tools + constitutional refuse | Anthropic serving |
| `system_fingerprint`, `finish_reason=stop`, `reasoning` field | OpenAI serving |
| `prompt_tokens` CJK matches Google tokenizer + grounding | Gemini |
| Error `1301` / Zhipu usage shape | GLM |
| Must-think every turn + DashScope errors | Qwen 2.4T-class |
| `reasoning_content` + cheap API + 中文 | DeepSeek R1/V4 |
| Long continuation addiction, 月之暗面 leaks | Kimi |
| xAI eval cadence, weak product-copy refuse | Grok |
| Llama Guard 400 + ChatML offset | Llama 4 + Guard wrapper |
| le Chat FR safety + tekken template | Mistral |
| Tokenizer=GLM, persona=Claude | **reseller costume** |
| Split-half JSD high | **rotating aggregator** |

---

<div align="center">

**ModelAudit** — *Because transparency is not optional.*

This project is maintained at github dot com slash Burak-ML slash ModelAudit
Author profile: github dot com slash Burak-ML

</div>

