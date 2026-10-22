# 04 - Final risk register (after human review)

> **Illustrative and fictional scenario** (Calder Fabrication Co. onboarding Vendor T, a SaaS TMS with AI features). Ratings are analyst judgment, not measured data. Corrected from the [raw AI output](02-raw-ai-output.md); every change is listed in [03-review-and-corrections.md](03-review-and-corrections.md). CSV: [04-final-risk-register.csv](04-final-risk-register.csv).

Scale: 1-5 likelihood x 1-5 impact; Critical 20-25, High 10-16, Medium 5-9, Low 1-4 (same as [ai-supply-chain-risk-assessment](https://github.com/Starboy1968/ai-supply-chain-risk-assessment)). Inherent = before controls; residual = existing controls only; target = after proposed controls. P/D/C = preventive/detective/corrective.

## Summary (ordered by residual score)

| ID | Risk | Inherent | Residual | Band | Within appetite? | Target | Owner | Review change |
|---|---|---|---|---|---|---|---|---|
| R1 | Carrier portal takeover redirects freight payments | 4x4=16 | 4x4=**16** | High | No (appetite low) | 4 | AP Manager | Residual credit removed (12 -> 16); KRI thresholds added |
| R3 | Customer data retained/trained on/breached at Vendor T or its model provider | 3x4=12 | 3x4=**12** | High | No (appetite low) | 6 | Third-Party Risk Manager (with Legal) | IDs verified against source |
| R5 | Poor AI load plans go unnoticed at peak | 3x4=12 | 3x4=**12** | High | No (appetite low for missed OEM windows) | 6 | Logistics Manager | Merged with draft R8 (duplicate); residual credit removed (9 -> 12) |
| R2 | AI freight audit approves bad invoices under $2,500 | 4x3=12 | 4x3=**12** | High | No (appetite low) | 6 | Controller | Invoice-splitting KRI added |
| R4 | Prompt injection makes the AI assistant draft false or disclosing messages | 3x3=9 | 3x3=**9** | Medium | No (appetite low for customer data) | 3 | Logistics Manager | Impact 5 -> 3 (inflated) |
| R7 | TMS outage at peak stops tendering | 3x4=12 | 3x3=**9** | Medium | No during peak (appetite low for missed OEM windows) | 6 | Third-Party Risk Manager (with Logistics Manager) | Appetite answer made explicit |
| R6 | Over-permissioned ERP API | 2x4=8 | 2x4=**8** | Medium | No (appetite low for customer data) | 4 | IT Integration Lead | Vague control rewritten; irrelevant existing control removed |
| R8 | No exit terms: data not returned or deleted | 2x4=8 | 2x4=**8** | Medium | No (appetite low for customer data) | 4 | Third-Party Risk Manager (with Legal) | New risk added in review |

## Full entries

### R1 - Carrier portal takeover redirects freight payments

**Risk statement:** Because carriers can change their own remit-to bank details in the Vendor T portal and there is no callback rule, an attacker who takes over a carrier's portal account may redirect freight payments, leading to direct financial loss and a dispute with the genuine carrier.

| Field | Value |
|---|---|
| Process/stage | Freight payment |
| Inherent | L4 x I4 = 16 |
| Existing controls | None that applies (ERP vendor-master approval may not cover portal changes - Q2) |
| Residual | L4 x I4 = **16 High** - within appetite: No (appetite low) |
| Proposed controls | Disable self-service bank changes in the portal; AP verifies every change by calling the carrier contact already on file, with dual approval (P); contract requires MFA on all carrier portal accounts (P); hold first payment to a changed account 5 business days and send confirmation to the address on file (D) |
| Owner | AP Manager |
| Target score | 4 |
| KRIs (threshold) | Bank changes not made through AP callback process: any = immediate review; >3 bank changes in a month = AP Manager review; % of changes verified by callback <100% |
| Framework reference | NIST CSF 2.0 GV.SC-05; Protect: identity management, authentication and access control |
| Assumptions / open questions | A1 portal MFA unknown; Q2 sync to ERP vendor master |
| Changed in review | Residual credit removed (12 -> 16); KRI thresholds added |

### R2 - AI freight audit approves bad invoices under $2,500

**Risk statement:** Because the AI freight audit auto-approves carrier invoices under $2,500 and replaces today's manual check, duplicate, inflated, split or fabricated invoices may be approved and exported to AP, leading to overpayment found late or not at all.

| Field | Value |
|---|---|
| Process/stage | Freight payment |
| Inherent | L4 x I3 = 12 |
| Existing controls | None (manual check is being replaced) |
| Residual | L4 x I3 = **12 High** - within appetite: No (appetite low) |
| Proposed controls | Recommend-only mode for first 90 days, freight analyst approves; then auto-approve only for carriers with 3 clean months (P); weekly re-check of 25 auto-approved invoices against BOL and rate confirmation, plus duplicate/split-invoice report before each payment run (D); contractual offset/clawback for overpayments (C) |
| Owner | Controller |
| Target score | 6 |
| KRIs (threshold) | Sample error rate >2%; unresolved duplicates >0; same carrier + same shipment/PRO invoices totaling >$2,500: >0 |
| Framework reference | NIST AI RMF Measure; NIST CSF 2.0 Detect: adverse event analysis |
| Assumptions / open questions | Q4 per-carrier threshold |
| Changed in review | Invoice-splitting KRI added |

### R3 - Customer data retained/trained on/breached at Vendor T or its model provider

**Risk statement:** Because customer ship-to contacts and order values flow every 15 minutes to Vendor T and on to an unnamed model provider, that data may be retained, used for training, or breached outside Calder's visibility, leading to notice obligations under OEM contracts and loss of customer trust.

| Field | Value |
|---|---|
| Process/stage | Order-to-ship data flow |
| Inherent | L3 x I4 = 12 |
| Existing controls | Annual SOC 2 report review (may not cover the model provider) |
| Residual | L3 x I4 = **12 High** - within appetite: No (appetite low) |
| Proposed controls | Contract: name all AI sub-processors, no training on Calder data, retention max 30 days, deletion on request, incident notice within 72 hours (P); send only fields the TMS needs (P); AI questions added to vendor questionnaire and model-provider attestation requested (D) |
| Owner | Third-Party Risk Manager (with Legal) |
| Target score | 6 |
| KRIs (threshold) | AI sub-processors not named in contract: >0 |
| Framework reference | NIST CSF 2.0 GV.SC-06; NIST AI RMF GOVERN 6.1; ISO/IEC 27001:2022 A.5.23 |
| Assumptions / open questions | A2 provider receives customer data; Q1 provider identity and terms; Q3 OEM notice windows |
| Changed in review | IDs verified against source |

### R4 - Prompt injection makes the AI assistant draft false or disclosing messages

**Risk statement:** Because the generative-AI assistant reads carrier emails and uploaded documents, hidden instructions in a document (prompt injection) may make it draft false status updates or include other customers' shipment details, leading to customer-facing errors or limited disclosure.

| Field | Value |
|---|---|
| Process/stage | Carrier communications |
| Inherent | L3 x I3 = 9 |
| Existing controls | Assistant drafts only (per vendor description); planners send |
| Residual | L3 x I3 = **9 Medium** - within appetite: No (appetite low for customer data) |
| Proposed controls | Confirm draft-only configuration; no auto-send; planner reviews every outbound message (P); request Vendor T prompt-injection test results and hidden-text handling for PDFs (D); ability to disable the assistant per site within 1 hour (C) |
| Owner | Logistics Manager |
| Target score | 3 |
| KRIs (threshold) | AI drafts edited or rejected by planners >10% in a week |
| Framework reference | OWASP LLM01:2025; NIST AI RMF Measure |
| Assumptions / open questions | None |
| Changed in review | Impact 5 -> 3 (inflated) |

### R5 - Poor AI load plans go unnoticed at peak

**Risk statement:** Because planners will rely on AI load building and carrier selection during peak, and the vendor's model provider can change or retire models without notice, poor-quality load plans may go unnoticed, leading to missed OEM delivery windows and higher freight cost.

| Field | Value |
|---|---|
| Process/stage | Load planning |
| Inherent | L3 x I4 = 12 |
| Existing controls | None that reduces this risk (fallback is not triggered if poor plans go unnoticed) |
| Residual | L3 x I4 = **12 High** - within appetite: No (appetite low for missed OEM windows) |
| Proposed controls | Planners approve every AI-built load for priority OEM customers during peak (P); contract requires 60 days' notice of material model changes (P); weekly on-time-delivery and cost-per-load comparison vs manual baseline, and re-test of sample loads after any model change (D) |
| Owner | Logistics Manager |
| Target score | 6 |
| KRIs (threshold) | OEM on-time delivery < baseline minus 3 points; unannounced model changes >0 per quarter |
| Framework reference | NIST AI RMF Measure; Manage |
| Assumptions / open questions | A2 provider and model-change terms unknown |
| Changed in review | Merged with draft R8 (duplicate); residual credit removed (9 -> 12) |

### R6 - Over-permissioned ERP API

**Risk statement:** Because the ERP-to-Vendor T API integration may be granted broader access than the TMS needs, a compromise of Vendor T or of the API credential may expose more ERP data than necessary or allow unwanted changes, leading to data exposure or corrupted orders.

| Field | Value |
|---|---|
| Process/stage | ERP integration |
| Inherent | L2 x I4 = 8 |
| Existing controls | None |
| Residual | L2 x I4 = **8 Medium** - within appetite: No (appetite low for customer data) |
| Proposed controls | Dedicated API service account limited to read of open orders and ship-to fields, write only to shipment status (P); IP allow-list for Vendor T (P); quarterly entitlement review (D); alert on >2x the 15-minute baseline volume or any non-allowed endpoint (D); rotate credential within 24 hours of any Vendor T incident notice (C) |
| Owner | IT Integration Lead |
| Target score | 4 |
| KRIs (threshold) | Calls >2x 15-minute baseline or to non-allowed endpoint: any |
| Framework reference | NIST CSF 2.0 Protect: identity management, authentication and access control |
| Assumptions / open questions | A3 API scope not yet defined |
| Changed in review | Vague control rewritten; irrelevant existing control removed |

### R7 - TMS outage at peak stops tendering

**Risk statement:** Because outbound tendering will run only through Vendor T, an outage of the TMS or its AI features during peak may stop load tendering, leading to shipment delays of more than one business day.

| Field | Value |
|---|---|
| Process/stage | Load tendering |
| Inherent | L3 x I4 = 12 |
| Existing controls | Manual spreadsheet planning (current process) |
| Residual | L3 x I3 = **9 Medium** - within appetite: No during peak (appetite low for missed OEM windows) |
| Proposed controls | SLA covering AI features and tendering, with service credits (P); half-day fallback drill using exported Vendor T data before peak (C) |
| Owner | Third-Party Risk Manager (with Logistics Manager) |
| Target score | 6 |
| KRIs (threshold) | TMS unavailable >2 business hours in a month |
| Framework reference | NIST CSF 2.0 Recover; Govern: supply chain risk management |
| Assumptions / open questions | Q5 fallback tested at peak volume? |
| Changed in review | Appetite answer made explicit |

### R8 - No exit terms: data not returned or deleted

**Risk statement:** Because the draft contract has no exit terms, at contract end or vendor failure Calder's customer data may remain with Vendor T and its model provider and freight history may not be exportable, leading to continued data exposure and a slow, costly switch.

| Field | Value |
|---|---|
| Process/stage | Contract / exit |
| Inherent | L2 x I4 = 8 |
| Existing controls | None |
| Residual | L2 x I4 = **8 Medium** - within appetite: No (appetite low for customer data) |
| Proposed controls | Exit clause: full data export in a defined format within 30 days, deletion certificate covering sub-processors (P); exit plan reviewed annually with the contract (D) |
| Owner | Third-Party Risk Manager (with Legal) |
| Target score | 4 |
| KRIs (threshold) | Critical SaaS contracts without exit/deletion clause: >0 |
| Framework reference | NIST CSF 2.0 GV.SC-10 |
| Assumptions / open questions | Assumes no exit terms in draft contract (verify with Legal) |
| Changed in review | New risk added in review |

## Open questions to close before contract signature

- Q1 Who is the model provider, where is data processed, and what are its retention and training terms? (Vendor T)
- Q2 Do portal bank changes sync to the ERP vendor master and its approval step? (Vendor T / AP Manager) - if yes, R1 residual may be revisited.
- Q3 Which OEM contracts carry incident-notice clauses, and what are the windows? (Legal)
- Q4 Can the auto-approval threshold be set per carrier? (Vendor T)
- Q5 Has the spreadsheet fallback been used at peak volume? (Logistics Manager)

Framework IDs above were checked against the primary sources listed in [03-review-and-corrections.md](03-review-and-corrections.md#framework-references-checked-against-the-primary-source); everything else is mapped at function level.
