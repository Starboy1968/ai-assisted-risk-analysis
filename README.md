# AI-Assisted Risk Analysis: Prompt Playbook and Worked Example

**A reusable GRC prompt for third-party and supply chain risk analysis, shown working on a realistic vendor-onboarding case: AI draft, human review, corrected risk register.**

## Summary

This project gives a risk analyst a tested prompt that turns a vendor or process description into a first-draft risk register: cause-event-consequence risk statements, 1-5 likelihood and impact ratings with rationale, inherent/residual/target scores, typed controls with owners, KRIs with thresholds, and open questions. It then shows the full workflow on an illustrative fictional case, a mid-size manufacturer onboarding a cloud transportation management system (TMS) that uses AI to build loads, audit freight invoices and draft carrier messages, including the review that found and fixed 11 problems in the AI's draft.

## Why it matters

AI writes a decent first draft of a risk assessment quickly. It also inflates ratings, credits controls that do not apply, repeats risks, writes vague controls, and cites framework IDs that do not exist. This project shows both halves: **AI for speed, analyst judgment for accuracy.**

## Prompt engineering

The prompt went through three versions on the same scenario. Each output was saved unedited, diagnosed, and the prompt revised.

- **[Prompt evolution](prompt-engineering/prompt-evolution.md):** v1 ("Identify the risks in this scenario and suggest controls") -> v2 (role, scale, fields) -> v3 (guardrails, defined scale, schema, self-check), with evidence from each run and one risk traced across all three.
- **[Techniques](prompt-engineering/techniques.md):** role-setting, structured inputs, defined scale and output schema, guardrails against invented facts, assumption flagging, iteration and self-check. Each one is tied to a concrete change and the effect it had.

| | v1 (naive) | v3 (final) |
|---|---|---|
| Risks | "Fraud Risk", "Compliance Risk" | "Because carriers can change their own remit-to bank details in the portal and there is no callback rule, an attacker who takes over a carrier's account may redirect freight payments..." |
| Ratings | None | 1-5 L x I with a rationale per rating; inherent, residual, target |
| Controls | "Implement strong access controls and MFA" | "AP verifies every bank change by calling the carrier contact already on file, with dual approval" |
| Frameworks | "NIST and ISO 27001" | Function level; IDs only when verified |

## Workflow

1. **Input:** fill in the structured inputs: org profile, scope, scenario, existing controls, risk appetite, frameworks. ([example](worked-example/01-input.md))
2. **AI draft:** run the [v3 prompt](prompt/risk-analysis-prompt.md) and save the output unedited. ([raw output](worked-example/02-raw-ai-output.md); earlier runs: [v1](worked-example/iteration-runs/run-1-v1-prompt-output.md), [v2](worked-example/iteration-runs/run-2-v2-prompt-output.md); [how the prompt evolved](prompt-engineering/prompt-evolution.md))
3. **Human review:** challenge every rating, control and framework reference; verify IDs against the primary source. ([review and corrections](worked-example/03-review-and-corrections.md))
4. **Final register:** the corrected register with owners, KRIs and open questions. ([Markdown](worked-example/04-final-risk-register.md) | [CSV](worked-example/04-final-risk-register.csv))

## Before / after: one corrected entry

R1, carrier bank-change fraud, as the AI returned it and after review:

| | AI draft (v3 run) | After human review |
|---|---|---|
| Existing control credited | "Vendor master changes require AP manager approval" | **None that applies.** Carriers change bank details in the vendor's portal; the AI's own open question (Q2) admits it is unknown whether those changes pass through the ERP approval step |
| Residual score | 3 x 4 = 12 (High) | **4 x 4 = 16 (High)**, outside the low appetite for payment fraud |
| KRI | "Number of carrier bank-detail changes per month", threshold "-" | Any change not made through the AP callback process = immediate review; more than 3 in a month = AP Manager review |
| Framework | "GV.SC-05 [verify ID]" | GV.SC-05 checked against the [NIST CSF 2.0 text](https://nvlpubs.nist.gov/nistpubs/CSWP/NIST.CSWP.29.pdf) and kept |

All 11 corrections, plus a "what the AI did vs what review added" table: [03-review-and-corrections.md](worked-example/03-review-and-corrections.md).

## Skills demonstrated

- **Prompt engineering for GRC:** role, structured inputs, defined scale, output schema, guardrails, self-check, and documented iteration ([evolution](prompt-engineering/prompt-evolution.md), [techniques](prompt-engineering/techniques.md))
- **Third-party and supply chain risk:** carrier portal fraud, AI invoice auto-approval, fourth-party (model provider) data exposure, exit and data return
- **Risk scoring:** 1-5 likelihood and impact with written rationale; inherent vs residual vs target; comparison to risk appetite
- **Control design:** specific preventive, detective and corrective controls with owner roles and KRIs with thresholds
- **Critical review of AI output:** inflated ratings, misapplied controls, duplicates, gaps, invalid framework IDs
- **Framework literacy:** NIST CSF 2.0, NIST AI RMF 1.0, ISO/IEC 27001:2022, OWASP Top 10 for LLM Applications 2025; IDs verified against source, otherwise mapped by function

## Repo map

| Path | What's inside |
|---|---|
| [prompt/](prompt/) | The final prompt with how-to, follow-up prompts and review checklist ([.md](prompt/risk-analysis-prompt.md)); copy-paste plain text ([.txt](prompt/risk-analysis-prompt.txt)) |
| [prompt-engineering/](prompt-engineering/) | [Prompt evolution v1 -> v3](prompt-engineering/prompt-evolution.md), [techniques](prompt-engineering/techniques.md), [v1](prompt-engineering/v1-prompt.txt) and [v2](prompt-engineering/v2-prompt.txt) prompts |
| [worked-example/](worked-example/) | [01 input](worked-example/01-input.md), [02 raw AI output](worked-example/02-raw-ai-output.md), [03 review and corrections](worked-example/03-review-and-corrections.md), [04 final register](worked-example/04-final-risk-register.md) ([CSV](worked-example/04-final-risk-register.csv)), [iteration runs](worked-example/iteration-runs/) |

> The worked example is **illustrative and fictional**: "Calder Fabrication Co." and "Vendor T" are made up. Ratings are analyst judgment, not measured data. All three AI runs were generated with an AI assistant and saved unedited; the review was done as part of this project's process and is pending the author's sign-off.

## Related projects

- [AI Supply Chain Risk Assessment](https://github.com/Starboy1968/ai-supply-chain-risk-assessment): nine scored AI-related supply chain risks, with controls, warning indicators (KRIs) and a 30/60/90-day plan.
- [Reading the Room: Power and Behavior Risk Indicators](https://github.com/Starboy1968/power-behavior-risk-indicators): observable signs of how power, incentives and group behavior shape risk decisions, and how to turn them into solid findings.
- [Behavioral Supply Chain Risk: Human Factors, AI, and Audit](https://github.com/Starboy1968/behavioral-supply-chain-risk-audit-ai): how bias and pressure create supply chain risk, and how AI triage, audit evidence and human review help catch it.
- [Playing the Risk Game: Game Theory for Supply Chain Risk and AI Governance](https://github.com/Starboy1968/supply-chain-ai-governance-strategy): plain-language game theory that explains why supply chain and AI risks persist, and which incentives to change.
- [AI in Supply Chain Operations: A Strategist's Field Guide](https://github.com/Starboy1968/ai-in-supply-chain-operations): how a supply chain manager can use AI in six everyday jobs, and what to check before trusting it.
- [Healthcare Cyber Risk Program: An OSINT Case Study](https://github.com/Starboy1968/healthcare-cyber-risk-osint-case-study): a cyber risk program for a fictional regional health system, built only from public sources.
- [AI for CISA and IT Auditors: Creative Workflow Accelerators](https://github.com/Starboy1968/ai-for-it-auditors): nine practical ways AI can speed up IT audit work, from walkthroughs and SOC 2 review to auditing AI, with a human check at every step.

## Author

Rashon Alsberry, supply chain and third-party risk analyst - [starboy1968.github.io](https://starboy1968.github.io)
