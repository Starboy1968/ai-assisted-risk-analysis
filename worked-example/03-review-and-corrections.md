# 03 - Human-review step: review and corrections

> **Illustrative and fictional scenario.** This is the human-review step of the workflow: a critical pass over the [raw AI output](02-raw-ai-output.md) before anything goes into a register. The checks below are the ones actually performed while building this project (in the same working session as the AI runs). The corrected result is in [04-final-risk-register.md](04-final-risk-register.md).

## How the review was done

1. Read every rating rationale against the [inputs](01-input.md) and the [1-5 scale](../prompt/risk-analysis-prompt.md#rating-scale).
2. Checked residual scores against the rule "credit existing controls only".
3. Asked of every control: could the owner start this tomorrow without asking what it means?
4. Looked for duplicate events and for gaps (exit, data return, fourth party).
5. Checked every framework ID against the primary source, or reduced it to function level.

## Problems found and fixes applied

| # | Where | Problem found | Fix applied |
|---|---|---|---|
| 1 | R1 residual | Residual likelihood was cut from 4 to 3 by crediting "vendor master changes require AP manager approval". But carriers change bank details **in the Vendor T portal**, and the AI's own Q2 says it is unknown whether those changes ever pass through the ERP vendor master. Crediting a control that may not apply breaks rule 7. | Removed the credit. Residual = 4 x 4 = **16 High** (was 12). Credit can be restored only if Vendor T confirms portal changes route through ERP approval (Q2). |
| 2 | R4 impact | Impact 5 ("Severe") with the rationale "attacker could take over the assistant and cause severe damage". The inputs say the assistant **drafts** replies and status updates for planners; it has no payment or system-change authority. The rationale did not point to any input. | Impact lowered to **3** (Moderate: wrong customer-facing status or disclosure of limited shipment details). Score 15 High -> **9 Medium**, still outside the low appetite for customer data. |
| 3 | R5 and R8 | Duplicate event. R5 (poor AI load plans) and R8 (model change degrades load building) end in the same event and consequence; R8 is a *cause* of R5. Rule 5 not followed. | Merged R8 into R5 as a second cause. Kept the model-change notice and re-test controls under R5. R8 ID reused for the missing exit risk (see #6). |
| 4 | R5 residual | Residual impact cut from 4 to 3 by crediting the manual spreadsheet fallback. The risk is that poor plans go **unnoticed**; an unused fallback does not reduce that impact. | Removed the credit. Residual = 3 x 4 = **12 High** (was 9 Medium). |
| 5 | R6 control | "Apply least-privilege principles to the API integration and monitor for anomalies", owner "IT". Vague: no object, no frequency, no named role. Rule 4 not followed. | Replaced with: a dedicated API service account limited to read of open orders and ship-to fields, no write to ERP except shipment status; IP allow-list for Vendor T egress; quarterly entitlement review by IT Integration Lead; alert when call volume exceeds 2x the 15-minute baseline or hits a non-allowed endpoint. Owner: IT Integration Lead. |
| 6 | Missing risk | No risk for **exit/termination**: what happens to Calder's customer data (at Vendor T and the model provider) and freight history if the contract ends or Vendor T fails. The prompt's own review checklist names this gap. | Added **R8**: exit, data return and deletion. L2, I4, score 8 Medium. Controls: contract exit clause with data export format, deletion certificate covering sub-processors, and an exit plan reviewed annually. |
| 7 | R1 and R6 KRIs | R1 "bank-detail changes per month" had no threshold ("-") although the self-check requires one. R6 "API calls outside expected pattern: Any" did not define the pattern. | R1: any bank change not made through the AP callback process = immediate review; more than 3 changes in a month = review by AP Manager. R6: defined as more than 2x the 15-minute baseline volume or any call to a non-allowed endpoint. |
| 8 | R2 gap | Auto-approval under $2,500 invites **invoice splitting** (several invoices for one shipment, each under the limit). No KRI or control caught it. | Added KRI: invoices from the same carrier for the same shipment/PRO number whose total exceeds $2,500 (threshold > 0). Added to the duplicate-invoice report. |
| 9 | R6 existing controls | Credited "employee SSO with MFA". That control protects employee logins, not the API service credential. (Scores did not change, but the register would mislead an auditor.) | Removed. Existing controls for R6: none. |
| 10 | R7 appetite | "Borderline" is not an answer. Appetite is moderate for delays up to 1 day **but low for missed OEM windows during peak**, and the scenario is peak. | Changed to **Outside appetite during peak**. |
| 11 | R4 framework ref | "OWASP LLM01:2025 Prompt Injection" was given without "[verify ID]", against rule 1. | Verified (see below); kept. |

## Framework references checked against the primary source

Every ID kept in the final register was looked up. IDs the AI marked "[verify ID]" were confirmed or removed.

| Reference in draft | Checked against | Result |
|---|---|---|
| NIST CSF 2.0 GV.SC-05 (R1) | NIST CSWP 29, CSF 2.0 - <https://nvlpubs.nist.gov/nistpubs/CSWP/NIST.CSWP.29.pdf> | Exists: "Requirements to address cybersecurity risks in supply chains are established, prioritized, and integrated into contracts..." Fits the portal-MFA contract requirement. Kept. |
| NIST CSF 2.0 GV.SC-06 (R3) | Same document | Exists: "Planning and due diligence are performed to reduce risks before entering into formal supplier or other third-party relationships." Kept. |
| NIST CSF 2.0 GV.SC-10 (new R8) | Same document | Exists: provisions for "activities that occur after the conclusion of a partnership or service agreement". Added. |
| NIST AI RMF GOVERN 6.1 (R3) | NIST AI 100-1 - <https://doi.org/10.6028/NIST.AI.100-1> | Exists: "Policies and procedures are in place that address AI risks associated with third-party entities..." Kept. |
| ISO/IEC 27001:2022 A.5.23 (R3) | Official ISO page <https://www.iso.org/standard/27001> (text is paywalled); control title confirmed against the ISO/IEC 27002:2022 to 2013 cross-walk table published at <https://www.deepseas.com/wp-content/uploads/2024/09/ISO_27002_table_B1_Final.pdf> | "Information security for use of cloud services" (new in 2022). Kept, with the note that the title was checked against a secondary summary, not the paid standard. |
| OWASP LLM01:2025 (R4) | <https://genai.owasp.org/llm-top-10/> | "LLM01:2025 Prompt Injection". Kept. |

**Also checked: the v2 run (earlier prompt) cited IDs that do not exist in the current versions.** `ID.SC-2`, `ID.SC-4` and `PR.AC-1/PR.AC-4` are not in the CSF 2.0 document (searched the full text; the ID.SC and PR.AC categories were replaced in 2.0 by GV.SC and PR.AA). `A.15.1.2`, `A.12.4.1`, `A.17.1.1`, `A.9.2.3` and `A.18.1.4` are ISO/IEC 27001:**2013** numbers; the 2022 Annex A runs A.5 to A.8. This is why the v3 prompt forces function-level mapping unless an ID is verified. See [prompt evolution](../prompt-engineering/prompt-evolution.md).

## What the AI did vs what human review added

| Step | AI (v3 prompt) | Human review |
|---|---|---|
| Risk identification | 8 risks, all in cause-event-consequence form, tied to the inputs | Merged one duplicate (R5/R8); added the missing exit/data-return risk; added invoice splitting under R2 |
| Ratings | L and I with one-line rationales; inherent, residual and target scores | Corrected one inflated impact (R4: 5 -> 3) and two residuals that credited controls that do not apply (R1, R5) |
| Controls | Mostly specific, typed, with owners and timing | Rewrote one vague control (R6) and removed one irrelevant "existing control" |
| KRIs | 10 KRIs, most with thresholds | Added two missing thresholds and one new KRI |
| Framework mapping | Function level, with four IDs flagged "[verify ID]" and one unflagged | Checked six IDs against NIST, ISO and OWASP sources; added GV.SC-10 |
| Open questions | Five useful questions (Q2 exposed problem #1) | Used Q2 to remove a residual credit; kept all questions open |
| Accountability | A complete first draft in one pass; makes no final call | Final ratings owned by a named reviewer (sign-off below, pending) |

---

> ### Reviewer sign-off (to be completed by Rashon)
>
> **Status: pending.** The corrections above were made during this project's review step. Before using this example in interviews, Rashon should walk through each of the 11 corrections and the six framework checks, confirm or change each one, and be ready to explain the reasoning in their own words. Sign-off: ____________ Date: ________
