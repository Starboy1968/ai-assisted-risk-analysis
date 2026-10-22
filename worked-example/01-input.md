# 01 - Input (the scenario given to the AI)

> **Illustrative and fictional.** "Calder Fabrication Co." and "Vendor T" are made-up names for a worked example. No real company, vendor, incident or statistic is described.

These are the exact inputs pasted into the **Inputs** block of the prompt. The same scenario text was used for all three runs (v1, v2, v3) so the outputs can be compared.

```
Organization profile:
  Calder Fabrication Co. (fictional). US mid-size manufacturer of metal enclosures and
  brackets, about 600 employees, 3 plants and 1 distribution center. Ships to industrial
  OEM customers; about 40 contracted freight carriers. No payment card data. Holds customer
  ship-to contacts and order pricing. Some OEM customer contracts require notice of
  security incidents affecting their data.

Asset / process in scope:
  Outbound freight planning and freight-invoice payment, moving to "Vendor T", a cloud
  (SaaS) transportation management system (TMS). Vendor T will:
  - receive open orders, ship-to addresses, customer contacts and order values from our
    ERP via API every 15 minutes;
  - use an AI feature to build loads and choose carriers/routes;
  - use an AI "freight audit" feature that matches carrier invoices to shipments and
    auto-approves invoices under $2,500 for payment (we export approved invoices to AP);
  - offer a generative-AI assistant that reads carrier emails and uploaded documents
    (BOLs, rate confirmations) and drafts replies and status updates for planners;
  - host a carrier portal where carriers log in to accept loads, upload documents and
    update their remit-to (bank) details.
  Vendor T's sales deck says its AI features "use a leading third-party model provider".
  The provider is not named.

Scenario / change:
  Contract signature planned in 6 weeks; go-live before the Q1 peak season. Procurement
  wants a third-party risk assessment before signature.

Existing controls:
  - Employee SSO with MFA for internal apps (IT).
  - Annual vendor security questionnaire and SOC 2 report review for critical vendors
    (Third-Party Risk). Questionnaire has no AI questions.
  - AP three-way match for supplier (materials) invoices. Freight invoices today are
    checked manually by a freight analyst before payment.
  - Vendor master changes in ERP require AP manager approval; no callback rule.
  - No AI acceptable-use policy yet (draft in progress).
  - Manual load planning in spreadsheets is the current process (would be the fallback).

Risk appetite:
  Low for payment fraud and for exposure of customer data.
  Moderate for operational delays of up to 1 business day.
  Low for missing OEM customer delivery windows during peak.

Frameworks in use:
  NIST CSF 2.0 (security program), aligning to ISO/IEC 27001:2022 (not certified),
  NIST AI RMF 1.0 referenced in the draft AI policy.

Known constraints:
  Small security team (3 people). Contract terms still negotiable. Go-live date fixed by
  the business.
```
