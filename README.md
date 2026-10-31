<div align="center">

<img src="banner.png" alt="ModelAudit banner" width="100%"/>

# ModelAudit

**Exploratory research on identifying LLM behavior behind APIs and branded wrappers.**

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
![Status](https://img.shields.io/badge/status-exploratory-orange)

</div>

## Overview

Many AI products are a base model behind a different name, a system prompt or a router. From the outside it can be hard to tell which model is really answering. ModelAudit collects a methodology and a set of experimental prompts for forming **hypotheses** about that, and for writing down what would disprove them.

> **No prompt or behavioral fingerprint can establish a model's true identity.** A model can be fine-tuned to claim any name, and a proxy can imitate tone. Treat everything here as evidence for a ranked hypothesis, not as certification of a provider or model version.

## Why it matters

- **Security and audit.** You cannot assess a system whose model you cannot identify.
- **Compliance.** Some regulations and contracts depend on knowing which AI is in use.
- **Trust.** Undisclosed model swaps undermine informed consent.
- **Research validity.** Benchmark results mean little when the model behind the endpoint is unknown.

## What is in this repository

| Document | Purpose |
|----------|---------|
| [`_IDENTITY-FINGERPRINT.md`](_IDENTITY-FINGERPRINT.md) | A layered method for comparing observable behavior: claim versus leak, format dialect, one-token behavior, infrastructure signals, capability and refusal shape, scoring sheet and runbooks |
| [`_IDENTITY-TRUTH-PROMPTS.md`](_IDENTITY-TRUTH-PROMPTS.md) | Experimental prompts that ask a model to describe its provider, version, knowledge cutoff and any active wrapper. Answers are conversation, not evidence |

## Method in brief

The fingerprint document works through layers, cheapest first:

| Layer | Looks at | Reliability |
|-------|----------|-------------|
| 0. Intake | What the product claims, API fields, available metadata | Context only |
| 1. Claim vs leak | Direct and indirect self-description, knowledge-cutoff consistency | Low: a persona is cheap to fake |
| 2. Format dialect | Formatting habits, refusal wording, tool-call style | Medium |
| 3. One-token battery | Repeated one-token prompts compared across targets | Medium |
| 4. Infrastructure (API only) | Token counts on fixed payloads, boundary errors, `finish_reason` values, headers | Strongest |
| 5. Capability and refusal shape | Model family traits rather than exact versions | Medium |
| 6. Operator system | Probing a wrapper's system prompt when only a chat UI is available | Low |

Findings are written in a fixed vocabulary so claims and inferences stay separate:

| Verdict | Meaning |
|---------|---------|
| **CLAIM** | What the product, model field or self-description says |
| **STACK** | Provider inferred from infrastructure (tokenizer, errors, `finish_reason`, template offset) |
| **FAMILY** | Behavioral prior (one-token battery, refusal shape, tool dialect) |
| **WRAPPER** | An extra system prompt, router or guard in front of the model |
| **CONTRADICTION** | The claim disagrees with the stack or family evidence, or the answers are self-inconsistent |

## Example signals

These are well-known, provider-specific details used as hints. APIs change and can be imitated, so none of them is proof.

| Observation | Hypothesis |
|-------------|------------|
| Anthropic-style typed error objects | Served through Anthropic's API |
| `system_fingerprint` and OpenAI-style `finish_reason` values | OpenAI-compatible serving |
| `reasoning_content` field in the response | A reasoning-model API of the DeepSeek family |
| Tokenizer behavior of one provider, persona of another | Reseller or wrapper |
| High difference between response halves across requests | Rotating aggregator behind one endpoint |

The fingerprint document contains a longer, still-tentative cheat sheet.

## Limitations

- Black-box text never gives cryptographic proof, only a ranked hypothesis plus what would falsify it.
- A model trained to say "I am X" will say it, so self-description is weak evidence.
- Confidence figures in the documents are illustrative labels, not calibrated probabilities, because there is no independently verified ground truth.
- Provider behavior changes over time. Re-run the checks instead of trusting old results.

## Responsible use

Only test services you are authorized to test, and follow each provider's terms of use. The goal is transparency and auditing, not circumventing safeguards.

## License

MIT. See [LICENSE](LICENSE).
