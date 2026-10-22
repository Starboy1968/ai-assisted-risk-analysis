# Prompt evolution: v1 -> v2 -> v3

The same fictional scenario ([01-input.md](../worked-example/01-input.md)) was run through three versions of the prompt. Each output was saved as returned, diagnosed against what a risk register needs, and the prompt was revised. These are the project's iteration rounds; only runs that actually happened are shown.

| Version | Prompt | Saved output |
|---|---|---|
| v1 (naive) | [v1-prompt.txt](v1-prompt.txt) | [run 1](../worked-example/iteration-runs/run-1-v1-prompt-output.md) |
| v2 (structured) | [v2-prompt.txt](v2-prompt.txt) | [run 2](../worked-example/iteration-runs/run-2-v2-prompt-output.md) |
| v3 (final) | [risk-analysis-prompt.md](../prompt/risk-analysis-prompt.md) | [run 3 = 02-raw-ai-output.md](../worked-example/02-raw-ai-output.md), then [human review](../worked-example/03-review-and-corrections.md) |

---

## v1: "Identify the risks in this scenario and suggest controls."

### v1 diagnosis

Evidence is quoted from the [run 1 output](../worked-example/iteration-runs/run-1-v1-prompt-output.md).

| Weakness | Evidence from run 1 | Why it matters |
|---|---|---|
| Category labels, not risks | "Third-Party / Vendor Risk", "Compliance Risk", "Integration Risk" | A label cannot be owned or scored; no cause, event or consequence |
| No ratings at all | No likelihood, impact or priority anywhere | Nobody can tell what to fix first or compare with risk appetite |
| Generic controls | "Implement strong access controls and MFA", "Monitor the vendor on an ongoing basis", "Implement AI governance" | An owner cannot act on these; they fit any vendor |
| No owners, no KRIs | None given | Nothing to track after go-live |
| Vague framework claims | "Ensure compliance with relevant regulations and frameworks such as NIST and ISO 27001" | Unverifiable; names no function or control |
| Missed the specific facts | Never mentions the $2,500 auto-approval threshold's effect, the unnamed model provider, or the peak-season go-live | The inputs held the most important risk drivers |
| Sales tone | "the benefits of the AI-powered TMS should outweigh the risks" | Not the AI's call; no basis in the inputs |

**What v2 changed:** gave a role, a 1-5 scale with bands, required fields per risk (L, I, score, typed controls, owner, KRIs, framework), and a register table.

---

## v2: role + scale + required fields

See [v2-prompt.txt](v2-prompt.txt).

### v2 diagnosis

Evidence is quoted from the [run 2 output](../worked-example/iteration-runs/run-2-v2-prompt-output.md). v2 was a large step up (scored, owned, tabular), which made its remaining problems easier to see.

| Weakness | Evidence from run 2 | Why it matters |
|---|---|---|
| Framework IDs that do not exist in the current versions | "NIST CSF ID.SC-2, PR.AC-1"; "ISO 27001 A.15.1.2", "A.12.4.1", "A.17.1.1" | ID.SC and PR.AC are CSF 1.1 categories, not in CSF 2.0; A.15/A.12/A.17 are ISO/IEC 27001:2013 numbers. Checked against the [CSF 2.0 text](https://nvlpubs.nist.gov/nistpubs/CSWP/NIST.CSWP.29.pdf). An auditor would catch this immediately. |
| Undefined scale, so ratings had no rationale | Scale was "1 = very low, 5 = very high"; every risk shows numbers only, e.g. R5 prompt injection "Likelihood: 4, Impact: 5" | Ratings could not be challenged or defended |
| Duplicate risks | R2 "AI freight audit approves incorrect invoices" and R3 "Fraudulent invoices auto-approved" are the same event (bad invoice auto-approved) | Double-counts one exposure, splits ownership |
| No inherent vs residual | One score per risk; existing controls in the inputs (manual fallback, SOC 2 review) ignored | Cannot compare with appetite or show what new controls buy |
| Still-vague controls | "input filtering", "monitor vendor performance", "least privilege", owner "IT" / "Finance" | Not actionable; owners are departments, not roles |
| KRIs without thresholds | "number of bank detail changes", "vendor uptime" | A KRI with no trigger never triggers anything |
| No assumptions or questions | The unnamed model provider was treated as known; nothing asked of Vendor T | Hidden assumptions drive ratings silently |
| Missed fourth party | The unnamed model provider appears only in passing in R4 | The biggest unknown in the inputs was not analyzed |

**What v3 changed:** guardrails (no invented IDs/stats; function-level mapping unless verified; flag assumptions; ask when unsure), a defined scale with wording per level, mandatory one-line rationale, cause-event-consequence statements, inherent/residual/target with the rule "residual credits existing controls only", a specificity test for controls, KRIs with thresholds, a fixed CSV schema, and a self-check run before answering. Details in [techniques.md](techniques.md).

---

## v3: the final prompt

[prompt/risk-analysis-prompt.md](../prompt/risk-analysis-prompt.md) (plain text: [.txt](../prompt/risk-analysis-prompt.txt)).

Run 3 fixed most v2 problems: no invented IDs (four flagged "[verify ID]"), every rating has a rationale, inherent/residual/target are separate, controls are mostly specific, and it asked five useful questions. It still made mistakes that only a reviewer caught: two residual scores credited controls that do not apply, one inflated impact, one duplicate, one vague control, two KRIs without thresholds, and one missing risk (exit). See the [11 corrections](../worked-example/03-review-and-corrections.md). **The prompt raises the floor; it does not replace review.**

---

## One risk, three runs (carrier bank-change fraud)

| | v1 (run 1) | v2 (run 2) | v3 (run 3, raw) | Final (after review) |
|---|---|---|---|---|
| **Risk** | "Fraud Risk. Carriers can update their bank details in the portal, which could be abused." | "R1 Carrier bank detail fraud. A compromised carrier account could redirect payments." | "Because carriers can change their own remit-to bank details in the Vendor T portal and there is no callback rule, an attacker who takes over a carrier's portal account may redirect freight payments..." | Same statement |
| **Rating** | None | 4 x 5 = 20 Critical, no rationale | Inherent 4x4=16, rationale given; residual 3x4=12 crediting ERP vendor-master approval | Residual **4x4=16 High**: credit removed because portal changes may bypass the ERP approval (Q2) |
| **Controls** | "Implement strong access controls and MFA. Review bank detail changes." | "MFA on carrier portal; review of bank changes; recover funds through bank" | Disable self-service change; callback to number on file + dual approval; contract-required portal MFA; 5-day first-payment hold + confirmation notice | Same, unchanged |
| **Owner / KRI** | None | AP Manager; "number of bank detail changes" (no threshold) | AP Manager; one KRI had no threshold | Thresholds added: any change outside AP callback = immediate review; >3/month = review |
| **Framework** | "NIST and ISO 27001" | "ID.SC-2, PR.AC-1; A.15.1.2" (not valid in current versions) | GV.SC-05 [verify ID] + function level | GV.SC-05 verified against CSF 2.0 |
