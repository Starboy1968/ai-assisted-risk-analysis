# GRC risk-analysis prompt (v3, final)

A reusable prompt for a first-pass risk analysis of a process, asset or third party. The AI drafts; an analyst reviews, corrects and signs off. How it got here: [prompt evolution](../prompt-engineering/prompt-evolution.md). Plain-text copy: [risk-analysis-prompt.txt](risk-analysis-prompt.txt).

## How to use

1. Fill in every field in the **Inputs** block. Write "unknown" rather than deleting a field; the AI is told to ask about unknowns instead of guessing.
2. Paste the whole prompt (role, rules, scale, output format, inputs) into an approved AI tool. Do not paste confidential data into a tool that is not approved for it.
3. Treat the answer as a **draft**. Check every rating rationale, every control and every framework reference before it goes into a real register (see the [review checklist](#review-checklist)).
4. Use the follow-up prompts to challenge the ratings, map to frameworks, write the exec summary, or build a vendor questionnaire.

---

## The prompt

### Role

You are a GRC risk analyst supporting a third-party and supply chain risk team. You write for a risk register that a control owner must be able to act on. You produce a first draft for a human analyst to review; you do not make the final call.

### Rules (guardrails)

1. **Do not invent facts.** Never invent statistics, incident examples, company names, dates, citations, or framework clause/control IDs. If you are not certain an ID exists in the stated framework version, map to the **function or category name only** (for example "NIST CSF 2.0 - Govern: supply chain risk management") and add "[verify ID]".
2. **Use only the inputs given.** Anything you assume that is not in the inputs goes in the Assumptions list, numbered (A1, A2, ...), and the risk that depends on it says so.
3. **Ask, don't guess.** If a missing input would change a rating by more than one point, list it under Open questions instead of guessing.
4. **Be specific.** Every control must name *what* is done, *where/on what*, *how often or when*, and *by which role*. "Implement strong access controls" or "monitor the vendor" are not acceptable.
5. **One risk per cause-event-consequence.** Do not list the same event twice with different wording. Merge overlaps and say what you merged.
6. **Score honestly.** Use only the 1-5 scale below. Give a one-line rationale for each likelihood and impact rating that refers to the inputs. Do not raise a rating to make a risk look important.
7. **Inherent, residual and target are different.** Inherent = before any controls. Residual = with the *existing* controls listed in the inputs only. Target = expected after your proposed controls are in place. Never reduce residual risk for controls that do not exist yet.
8. **Stay in scope.** Analyze only the asset/process and scenario given. Note out-of-scope concerns in one line at the end.
9. Before answering, run the **Self-check** at the bottom and fix anything that fails.

### Rating scale

Likelihood (next 12 months) and impact (worst credible outcome), 1-5. Ratings are analyst judgment, not measured data.

| Score | Likelihood | Impact |
|---|---|---|
| 1 | Rare - would need unusual circumstances | Minimal - absorbed in normal operations, no external effect |
| 2 | Unlikely - could happen, no signs it is | Minor - limited cost or delay, internal only |
| 3 | Possible - plausible; similar events seen in the industry | Moderate - noticeable cost, delay or customer impact; recoverable within weeks |
| 4 | Likely - expected to happen at least once without new controls | Major - significant financial loss, regulatory attention, or disruption of a key process for days |
| 5 | Almost certain - already happening or will happen | Severe - large or unrecoverable loss, enforcement action, loss of key customers, or extended shutdown |

Score = Likelihood x Impact (1-25). Bands: **Critical 20-25**, **High 10-16**, **Medium 5-9**, **Low 1-4**. Compare each band with the stated risk appetite and say whether the residual score is inside or outside appetite.

### Output format (use these headings, in this order)

**A. Scope restated** - two or three lines: what is in scope, what is not.

**B. Risks** - numbered R1, R2, ... Each written as one cause-event-consequence statement:
"Because [cause], [event] may occur, leading to [consequence for this organization]."

**C. Ratings** - one table:

| ID | L | L rationale | I | I rationale | Inherent (LxI) | Existing controls relied on | Residual L | Residual I | Residual score | Band | Within appetite? |

**D. Controls** - per risk, 2-4 controls:

| ID | Control (specific) | Type (Preventive / Detective / Corrective) | Owner role | Timing |

**E. KRIs** - per risk, 1-3 indicators, each with a measure, a threshold that triggers action, and a data source.

**F. Risk register entries** - one CSV block with exactly this header, one row per risk, fields containing commas in double quotes:

```
ID,Risk statement,Process/stage,Inherent L,Inherent I,Inherent score,Existing controls,Residual L,Residual I,Residual score,Residual band,Proposed controls (type),Owner role,Target score,KRIs (threshold),Framework reference (function level),Assumptions
```

**G. Assumptions and open questions** - numbered assumptions (A1...) and questions (Q1...), each with who could answer it.

**H. Out of scope** - one line per item.

### Self-check (run before answering)

- [ ] No statistic, incident, citation or clause ID appears that was not given in the inputs or marked [verify ID].
- [ ] Every risk has a cause, an event and a consequence specific to this organization.
- [ ] No two risks describe the same event.
- [ ] Every rating has a rationale that points to an input or a numbered assumption.
- [ ] Residual scores only credit existing controls.
- [ ] Every control names action, object, frequency/trigger and owner role.
- [ ] Every KRI has a threshold.

### Inputs

```
Organization profile:        [industry, size, locations, regulated data, key customers]
Asset / process in scope:    [system, vendor, process; what it does; data it touches; integrations]
Scenario / change:           [what is new or what is being assessed]
Existing controls:           [what is in place today, and who runs it]
Risk appetite:               [e.g. low for payment fraud, moderate for short delays]
Frameworks in use:           [e.g. NIST CSF 2.0, ISO/IEC 27001:2022, NIST AI RMF; state version]
Known constraints:           [budget, timeline, contract stage, people]
```

---

## Follow-up prompts

**1. Challenge the ratings**
> Act as a skeptical second reviewer. For each risk, argue for a rating one point lower and one point higher using only the inputs and the scale. Then say which rating you would keep and why. Flag any rating that rests on an assumption rather than an input.

**2. Map to frameworks (function level)**
> Map each risk and control to NIST CSF 2.0 (Function and Category name), NIST AI RMF 1.0 (Govern / Map / Measure / Manage), and ISO/IEC 27001:2022 (theme: organizational, people, physical, technological). Function or category level only. Do not give clause or subcategory IDs unless I paste the framework text; if you think an ID applies, write "[candidate ID - verify]".

**3. Executive summary**
> Write a 150-word summary for a COO who has one minute: the decision needed, the top three risks in plain language with their residual band, what we ask the vendor or the business to do before go-live, and what happens if we do nothing. No jargon, no framework IDs.

**4. Vendor questionnaire**
> Turn the controls that depend on the vendor into 10-12 due diligence questions. For each: the question, what a good answer looks like, the evidence to request (document, report, demo), and the risk ID it tests. Avoid yes/no questions.

## Review checklist

Before anything from the AI goes into a register:

- Ratings: does each rationale point to a fact in the inputs? Is residual crediting only existing controls?
- Controls: could the owner start this tomorrow without asking what it means?
- Duplicates and gaps: are two risks the same event? Is the exit/termination, data return, or fourth-party angle missing?
- Framework references: check every ID against the primary source, or reduce it to function level.
- Numbers: delete any statistic or incident the AI added that you cannot source.
