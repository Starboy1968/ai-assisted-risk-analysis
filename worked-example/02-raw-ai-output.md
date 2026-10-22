# 02 - Raw AI output (run 3, final v3 prompt)

> **Illustrative and fictional scenario.** Prompt: [prompt/risk-analysis-prompt.md](../prompt/risk-analysis-prompt.md) (v3) with the inputs in [01-input.md](01-input.md). This is the first-pass output **exactly as returned, unedited**, including its mistakes. The problems found in it are listed in [03-review-and-corrections.md](03-review-and-corrections.md). Earlier runs with weaker prompts: [run 1 (v1)](iteration-runs/run-1-v1-prompt-output.md), [run 2 (v2)](iteration-runs/run-2-v2-prompt-output.md).

---

## A. Scope restated

In scope: onboarding Vendor T's SaaS TMS for outbound freight planning and freight-invoice payment at Calder Fabrication Co., including its AI load building, AI freight audit (auto-approval under $2,500), generative-AI assistant, carrier portal, and the 15-minute ERP API feed. Out of scope: inbound/materials procurement, Calder's internal network, and carriers' own systems except where they touch the portal.

## B. Risks

- **R1** Because carriers can change their own remit-to bank details in the Vendor T portal and there is no callback rule, an attacker who takes over a carrier's portal account may redirect freight payments to an attacker account, leading to direct financial loss and a dispute with the genuine carrier.
- **R2** Because the AI freight audit auto-approves carrier invoices under $2,500 and replaces today's manual check, duplicate, inflated or fabricated invoices may be approved and exported to AP, leading to overpayment that is found late or not at all.
- **R3** Because customer ship-to contacts and order values flow every 15 minutes to Vendor T and on to an unnamed third-party model provider, that data may be retained, used for model training, or exposed in a breach outside Calder's visibility, leading to breach-notice obligations under OEM customer contracts and loss of customer trust.
- **R4** Because the generative-AI assistant reads carrier emails and uploaded documents, a carrier or attacker may embed instructions in a document (prompt injection) that cause the assistant to draft false status updates or include other customers' shipment details in replies, leading to customer-facing errors or disclosure.
- **R5** Because planners will rely on AI load building and carrier selection during peak, poor-quality load plans may go unnoticed, leading to missed OEM delivery windows and higher freight cost.
- **R6** Because the ERP-to-Vendor T API integration may be granted broader access than the TMS needs, a compromise of Vendor T or of the API credentials may expose more ERP data than necessary or allow unwanted changes, leading to data exposure or corrupted orders.
- **R7** Because outbound tendering will run only through Vendor T, an outage of the TMS or its AI features during peak may stop load tendering, leading to shipment delays of more than one business day.
- **R8** Because Vendor T's AI features depend on a third-party model provider that can change or retire models, an unannounced model change may degrade load building and invoice matching, leading to higher cost and service failures.

## C. Ratings

| ID | L | L rationale | I | I rationale | Inherent | Existing controls relied on | Res. L | Res. I | Residual | Band | Within appetite? |
|---|---|---|---|---|---|---|---|---|---|---|---|
| R1 | 4 | Self-service bank change for ~40 carriers, no callback; portal MFA unknown (A1) | 4 | One diverted payment run to a large carrier is a significant loss; recovery uncertain | 16 | Vendor master changes require AP manager approval | 3 | 4 | 12 | High | No (appetite low) |
| R2 | 4 | Manual check removed; auto-approval runs on every invoice under threshold | 3 | Individual losses capped below $2,500 but can repeat unnoticed for weeks | 12 | None (manual check is being replaced) | 4 | 3 | 12 | High | No (appetite low) |
| R3 | 3 | Fourth-party provider unnamed; retention/training terms unknown (A2) | 4 | OEM contracts require incident notice; customer trust and contract exposure | 12 | Annual SOC 2 review of critical vendors | 3 | 4 | 12 | High | No (appetite low) |
| R4 | 3 | Assistant processes external carrier content daily; no input controls stated | 5 | Attacker could take over the assistant and cause severe damage | 15 | None | 3 | 5 | 15 | High | No |
| R5 | 3 | New tool, peak season, planners new to AI plans | 4 | Missed OEM delivery windows during peak; appetite is low | 12 | Manual spreadsheet planning as fallback | 3 | 3 | 9 | Medium | No (appetite low for missed windows) |
| R6 | 2 | API scope not yet defined (A3); requires vendor-side compromise | 4 | Exposure of customer and order data; possible order corruption | 8 | Employee SSO with MFA | 2 | 4 | 8 | Medium | No (appetite low for customer data) |
| R7 | 3 | SaaS outages occur; peak load increases impact | 4 | Tendering stops; delays beyond 1 day likely during peak | 12 | Manual spreadsheet planning as fallback | 3 | 3 | 9 | Medium | Borderline (moderate appetite up to 1 day) |
| R8 | 3 | Provider unnamed; no change-notice terms (A2) | 3 | Degraded plans and matching; recoverable within weeks | 9 | None | 3 | 3 | 9 | Medium | No |

## D. Controls

| ID | Control (specific) | Type | Owner role | Timing |
|---|---|---|---|---|
| R1 | Disable self-service remit-to changes in the portal; route all bank changes to AP, verified by calling the carrier contact on file (not from the request), with dual approval | Preventive | AP Manager | Before go-live; every change |
| R1 | Require MFA for all carrier portal accounts (contract requirement on Vendor T) | Preventive | Third-Party Risk Manager | Contract signature |
| R1 | Hold the first payment to any changed bank account for 5 business days and send a change-confirmation notice to the carrier's address on file | Detective | AP Manager | Every change |
| R2 | Set auto-approval to "recommend only" for the first 90 days; freight analyst approves; then auto-approve only for carriers with 3 clean months | Preventive | Controller | Go-live + 90 days |
| R2 | Weekly sample of 25 auto-approved invoices re-checked against BOL and rate confirmation; duplicate-invoice report (same carrier, PRO number, amount) before each payment run | Detective | Freight Analyst, reviewed by Controller | Weekly / each payment run |
| R2 | Contract right to claw back overpayments from carriers via offset | Corrective | Logistics Manager with Legal | Carrier contract renewals |
| R3 | Contract clauses: name all AI sub-processors, no training on Calder data, prompt/output retention max 30 days, deletion on request, incident notice within 72 hours | Preventive | Third-Party Risk Manager with Legal | Before signature |
| R3 | Send only fields the TMS needs (no order value if not required for audit) | Preventive | IT Integration Lead | Before go-live |
| R3 | Add AI questions to the vendor questionnaire and request the model provider's attestation | Detective | Third-Party Risk Manager | Before signature; annually |
| R4 | Assistant may draft only; no auto-send; planner reviews every outbound message | Preventive | Logistics Manager | Go-live |
| R4 | Ask Vendor T for prompt-injection test results and how hidden text in PDFs is handled | Detective | Security Lead | Before signature |
| R4 | Ability to disable the assistant per site within 1 hour | Corrective | IT Application Owner | Go-live |
| R5 | Planners approve every AI-built load for OEM priority customers during peak | Preventive | Logistics Manager | Peak season |
| R5 | Weekly on-time-delivery and cost-per-load comparison vs prior manual baseline | Detective | Logistics Analyst | Weekly |
| R6 | Apply least-privilege principles to the API integration and monitor for anomalies | Preventive | IT | Ongoing |
| R6 | Rotate API credentials if Vendor T reports an incident | Corrective | IT | On incident |
| R7 | SLA covering AI features and tendering, with service credits | Preventive | Third-Party Risk Manager | Contract signature |
| R7 | Test the spreadsheet fallback with a half-day drill before peak | Corrective | Logistics Manager | Before peak |
| R8 | Contract requires 60 days' notice of material model changes | Preventive | Third-Party Risk Manager | Contract signature |
| R8 | Re-run a sample of loads and invoices after any model change | Detective | Logistics Analyst | On change |

## E. KRIs

| ID | KRI | Threshold | Data source |
|---|---|---|---|
| R1 | Number of carrier bank-detail changes per month | - | Vendor T portal audit log |
| R1 | % of bank changes verified by callback | < 100% | AP change log |
| R2 | Error rate in weekly sample of auto-approved invoices | > 2% | Freight analyst sample |
| R2 | Duplicate invoices flagged per payment run | > 0 unresolved | Duplicate report |
| R3 | AI sub-processors not named in contract | > 0 | Contract register |
| R4 | AI-drafted messages edited or rejected by planners | > 10% in a week | Vendor T assistant log |
| R5 | On-time delivery to OEM customers | < baseline - 3 points | TMS / ERP |
| R6 | API calls outside expected pattern | Any | API gateway logs |
| R7 | TMS unavailability during business hours | > 2 hours in a month | Vendor status page / incident log |
| R8 | Unannounced model changes | > 0 per quarter | Vendor notices |

## F. Risk register entries

```csv
ID,Risk statement,Process/stage,Inherent L,Inherent I,Inherent score,Existing controls,Residual L,Residual I,Residual score,Residual band,Proposed controls (type),Owner role,Target score,KRIs (threshold),Framework reference (function level),Assumptions
R1,"Carrier portal account takeover redirects freight payments via self-service bank change",Freight payment,4,4,16,"Vendor master changes require AP manager approval",3,4,12,High,"Disable self-service bank change + callback + dual approval (P); portal MFA (P); first-payment hold + confirmation notice (D)",AP Manager,4,"% bank changes verified by callback <100%","NIST CSF 2.0 Protect: identity management, authentication and access control; Govern: supply chain risk management - GV.SC-05 [verify ID]",A1
R2,"AI freight audit auto-approves duplicate, inflated or fabricated invoices under $2,500",Freight payment,4,3,12,None,4,3,12,High,"Recommend-only for 90 days (P); weekly sample + duplicate report (D); clawback right (C)",Controller,6,"Sample error rate >2%; unresolved duplicates >0","NIST AI RMF Measure; NIST CSF 2.0 Detect: adverse event analysis",-
R3,"Customer data sent to Vendor T and unnamed model provider is retained, trained on or breached",Order-to-ship data flow,3,4,12,"Annual SOC 2 review",3,4,12,High,"AI sub-processor, no-training, retention and notice clauses (P); data minimization (P); AI questionnaire (D)",Third-Party Risk Manager,6,"Unnamed AI sub-processors >0","NIST CSF 2.0 GV.SC-06 [verify ID]; NIST AI RMF GOVERN 6.1 [verify ID]; ISO/IEC 27001:2022 A.5.23 [verify ID]",A2
R4,"Prompt injection in carrier documents makes the assistant draft false or disclosing messages",Carrier communications,3,5,15,None,3,5,15,High,"Draft-only, human review (P); vendor injection testing (D); kill switch (C)",Logistics Manager,6,"Drafts edited/rejected >10% weekly","OWASP LLM01:2025 Prompt Injection; NIST AI RMF Measure",-
R5,"Poor AI load plans go unnoticed at peak, missing OEM delivery windows",Load planning,3,4,12,"Manual spreadsheet fallback",3,3,9,Medium,"Planner approval for priority OEM loads (P); weekly OTD/cost comparison (D)",Logistics Manager,6,"OTD < baseline -3 pts","NIST AI RMF Measure; Manage",-
R6,"Over-permissioned ERP API exposes extra data or allows unwanted changes",ERP integration,2,4,8,"Employee SSO with MFA",2,4,8,Medium,"Least privilege and monitoring (P); credential rotation (C)",IT,4,"API calls outside expected pattern: any","NIST CSF 2.0 Protect: identity management, authentication and access control",A3
R7,"TMS or AI outage at peak stops tendering for more than 1 business day",Load tendering,3,4,12,"Manual spreadsheet fallback",3,3,9,Medium,"SLA incl. AI features (P); fallback drill (C)",Third-Party Risk Manager,6,"Unavailability >2 h/month","NIST CSF 2.0 Recover; Govern: supply chain risk management",-
R8,"Unannounced model change degrades load building and invoice matching",Load planning; freight audit,3,3,9,None,3,3,9,Medium,"60-day model change notice (P); re-test after change (D)",Third-Party Risk Manager,4,"Unannounced changes >0/quarter","NIST AI RMF Manage",A2
```

## G. Assumptions and open questions

**Assumptions**
- A1 Carrier portal accounts may not enforce MFA today. (Vendor T)
- A2 The unnamed model provider receives prompts containing shipment and customer data. (Vendor T)
- A3 The API scope has not been defined yet. (IT Integration Lead)

**Open questions**
- Q1 Who is the model provider, where is data processed, and what are its retention and training terms? (Vendor T)
- Q2 Do bank changes made in the portal sync to the ERP vendor master, or are they paid from Vendor T data? (Vendor T / AP Manager)
- Q3 Which OEM contracts carry incident-notice clauses, and what are the notice windows? (Legal)
- Q4 Can the auto-approval threshold be set per carrier? (Vendor T)
- Q5 Has the spreadsheet fallback been used at peak volume? (Logistics Manager)

## H. Out of scope

- Carriers' own cybersecurity programs (beyond portal access).
- Inbound freight and materials suppliers.
- Vendor T's financial viability (recommend a separate financial review).
