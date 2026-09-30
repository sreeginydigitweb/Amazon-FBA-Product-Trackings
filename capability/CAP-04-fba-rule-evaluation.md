# Name

CAP-04 — FBA Rule Evaluation Governance

# Purpose

Govern how the FBA eligibility rules are later implemented and applied, so the
decision for each ASIN is reproducible, explainable, and traceable to the
original requirement.

**The rule engine is NOT implemented by this file and must not be implemented
during the SKILL stage.** This is the governing capability only.

# Responsibility

One responsibility: **define how rules are evaluated and how outcomes are
justified.**

It does not source data (CAP-02), does not decide what a column means (CAP-03),
does not render (CAP-05).

# Inputs

- A field map from CAP-03 in which **every rule input is VERIFIED**
- Rule definitions and thresholds from `AMAZON FBA.pdf` (canonical)
- Exclusion and verification requirements from `AMAZON FBA.pdf`

# Outputs

- Per ASIN: one outcome, plus the reason — which rule decided it, and the input
  values that drove it
- A rule-trace record so any outcome can be re-derived later
- A list of ASINs excluded, with the exclusion reason
- A notification list for `FBA Eligible` outcomes
- A refusal record for any ASIN whose inputs were not VERIFIED

# Allowed Actions

- Evaluate an ASIN **only** when every input the rules need is VERIFIED
- Return `INSUFFICIENT DATA` for an ASIN with a missing or unverified input
- Record which rule fired, and the values behind it
- Reconcile the thresholds recorded below against `AMAZON FBA.pdf` and correct
  them to match the PDF
- Flag a rule in the PDF that is ambiguous, and ask

# Prohibited Actions

- Implementing the rule engine during the SKILL stage
- Evaluating an ASIN on UNVERIFIED, UNKNOWN, NOT FOUND, or defaulted input
- Substituting a default (0, null, "N/A") for a missing input and evaluating
  anyway — a missing input is `INSUFFICIENT DATA`, never a fail and never a pass
- Changing a threshold to make more products qualify
- Inventing a rule, tie-breaker, or precedence order that is not in the PDF
- Rounding or re-scaling a value to cross a threshold
- Using the prototype HTML's sample outcomes as expected results
- Hard-coding a reporting week, or a static 30-day sales value, as rule input
- Sending any real notification during Build or validation

# Source Boundaries

`AMAZON FBA.pdf` is the **only** authority for rules, thresholds, exclusions and
notification conditions. Verified LEDSone data supplies the inputs.
`FBA Selection Tracking 2.html` contributes nothing to rule logic — its sample
statuses are decoration, not test fixtures.

**Threshold reconciliation is required before Build.** The thresholds recorded
below were supplied in the SKILL-stage brief. `AMAZON FBA.pdf` was deliberately
not opened during this stage, so they are **UNVERIFIED against the PDF**. During
Analysis they must be checked line by line against the PDF; **the PDF wins on
any conflict**, and this file is then corrected.

# Business/Data Rules

Recorded as supplied — **UNVERIFIED against `AMAZON FBA.pdf`**:

**STEP 0 — precedence gate, evaluated first**

| Condition | Outcome |
|---|---|
| Already in FBA | `No Action` — stop, do not evaluate qualification |

**Qualification criteria** (applied only when STEP 0 did not fire)

| Criterion | Threshold | Boundary reading |
|---|---|---|
| Rating | `>= 4` stars | 4 passes |
| 30-day sales | `>= 5` | 5 passes; rolling window, never static |
| Net margin | `>= 45%` | 45 passes; formula per PDF |
| Stock | `> 20` | 20 does **not** pass — see stock band below |

**Stock band**

| Condition | Outcome |
|---|---|
| `Stock > 20` | Stock criterion passes |
| `Stock <= 20` | `Review Stock` |

**Outcomes**

| Situation | Outcome |
|---|---|
| Already in FBA | `No Action` |
| Any qualification criterion fails | `Not Eligible / Do Not Select` |
| Stock `<= 20` | `Review Stock` |
| All criteria pass | `FBA Eligible` -> `Send Notification` |
| An input is not VERIFIED | `INSUFFICIENT DATA` |

**Open questions to settle from the PDF during Analysis** — do not resolve by
assumption:

1. Exact precedence when `Stock <= 20` **and** another criterion also fails: is
   the outcome `Review Stock` or `Not Eligible`?
2. Whether `Review Stock` is terminal, or re-enters qualification once stock
   recovers.
3. Whether exclusions (fragile/crystal, overweight/oversized, return history,
   damage history) are evaluated **before** qualification as hard excludes, or
   alongside it — and their exact thresholds.
4. Which rating applies to the rule: per-ASIN rating or account-level rating.
   CAP-03 keeps them separate for this reason.
5. The exact net margin formula, and whether it is pre- or post-FBA-fee.
6. Notification recipient, channel, and trigger timing.

Cross-cutting rules:

- **Every outcome is explainable.** An outcome with no recorded reason is a
  defect.
- **Deterministic.** The same inputs must always give the same outcome.
- **Dynamic dates.** The 30-day window is computed from the verified date field
  at run time. A resolved week must never be stored as logic.
- **Amazon only, "PH holder Dilani" only.** Rules never run on out-of-scope
  products. Scope is applied before evaluation, using CAP-03's verified mapping.

# Validation Rules

1. Every threshold reconciled against `AMAZON FBA.pdf`, and this file corrected
2. Every rule input is VERIFIED in `data-maps/`
3. STEP 0 is evaluated before qualification, and short-circuits
4. Boundary values tested explicitly: rating exactly 4; sales exactly 5; margin
   exactly 45%; stock exactly 20 and exactly 21
5. A missing input yields `INSUFFICIENT DATA`, never a pass or a fail
6. Every outcome carries its deciding rule and input values
7. The 6 open questions above are answered from the PDF, not assumed
8. No hard-coded week or static sales figure in the logic
9. Scope filters applied before evaluation
10. No notification actually sent during Build or validation

# Failure / Blocker Behaviour

- A threshold cannot be confirmed from the PDF → **BLOCKED** on that rule. Do
  not adopt the brief's value as final; report the discrepancy.
- An input is not VERIFIED → return `INSUFFICIENT DATA` for that ASIN, evaluate
  the others, and report how many were skipped and why.
- Precedence is ambiguous in the PDF → **BLOCKED**, with the specific ambiguity
  and both candidate readings. Do not pick one.
- Outcomes disagree with the prototype HTML's samples → that is **not** a
  failure. The prototype is not a source of truth. Note it and move on.

# Handoff to Next Stage

Hand CAP-05 a per-ASIN outcome set where each row carries its outcome, deciding
rule, and input values — plus the count of `INSUFFICIENT DATA` rows, which the
dashboard must show honestly rather than hide.
