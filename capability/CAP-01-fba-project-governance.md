# Name

CAP-01 — FBA Project Governance (stage gate)

# Purpose

Keep the Amazon FBA Product Selection & Tracking project moving through its
stages in order, and stop any stage from consuming work that has not yet been
verified. This capability exists because the project's failure mode is building
a plausible dashboard on guessed data, not writing bad code.

This capability is **subordinate to the Mini-AIOS `new-joiner-guide` skill**,
which is the organisation-level source of truth for folder standards, GREEN /
AMBER / RED risk levels, the Evidence Rule, Existing-Asset-First, the
Unknown-Developer-Test, and escalation owners. Those rules are **not restated
here**. Read that skill for them. CAP-01 adds only what is specific to this
project.

# Responsibility

One responsibility: **enforce stage order and fact discipline for this project.**

It does not map data (CAP-03), evaluate rules (CAP-04), build UI (CAP-05), or
touch the database (CAP-02).

# Inputs

- The current stage declared by the user at the start of a session
- `handover/` — what the previous stage recorded as done, pending, blocked
- `validation/` — whether the previous stage actually passed
- The original requirement: `AMAZON FBA.pdf` (project root, canonical)

# Outputs

- A stage gate decision: PROCEED or BLOCKED, with the reason
- A stage record written to `validation/` (checks) and `closure/` (acceptance)
- A handover note in `handover/` naming the next stage and its open questions
- Every claim in project documents labelled **FACT**, **ASSUMPTION**, or
  **UNVERIFIED**

# Allowed Actions

- Read any project folder and any canonical reference
- Declare a stage PASS only when its own validation checks have actually run
- Refuse to start a stage whose prerequisite stage has not passed
- Label, downgrade, or reject an unsupported claim in project documentation
- Escalate per `new-joiner-guide` when a decision is not the project's to make

# Prohibited Actions

- Running more than the one stage the user has declared current
- Auto-advancing to the next stage without the user asking
- Recording PASS for a check that was not run
- Presenting an ASSUMPTION as a FACT, or dropping the label once applied
- Restating or overriding `new-joiner-guide` organisation rules
- `git commit`, `git push`, or any deployment, at any stage, unless the user
  explicitly asks in that session

# Source Boundaries

Reference priority — **higher wins on conflict, always**:

| # | Source | Status |
|---|---|---|
| 1 | `AMAZON FBA.pdf` (project root) | Canonical business requirement |
| 2 | Verified LEDSone source data | Canonical data truth, once verified |
| 3 | `FBA Selection Tracking 2.html` (project root) | UI / layout reference **only** |

`FBA Selection Tracking 2.html` contains prototype sample products. Those rows
are **never** production data and never evidence of anything.

Downloads copies of either reference are **not** working references. See
`duplicate-risk-reports/DRR-01-canonical-references.md`.

# Business/Data Rules

Stage order, one stage per session:

```
Folder Structure -> SKILL -> Discovery -> Analysis -> Build Features
  -> Skill Validation -> Fix -> Final Validation -> Task Complete
```

Gates that cannot be crossed:

- **No Analysis before Discovery has verified the schema.** Analysis on guessed
  tables is not analysis.
- **No Build before Analysis has produced a verified field map** in
  `data-maps/` and reconciled rules in `documentation/`.
- **No Final Validation until every requirement traces** to a specific
  requirement line in `AMAZON FBA.pdf`.
- Nothing is "done" without evidence in `evidence/` (Evidence Rule, inherited).

Traceability: every built feature carries a pointer back to the requirement it
satisfies. A feature with no requirement is scope creep and gets removed, not
kept.

No unnecessary redesign: where the supplied reference UI already answers a
layout question, follow it rather than improving it.

# Validation Rules

Before any stage may be declared PASS:

1. The stage's own validation checklist exists in `validation/` and was run
2. Every claim is labelled FACT / ASSUMPTION / UNVERIFIED
3. Supporting evidence exists in `evidence/` and is referenced by path
4. No prohibited action in this file was performed
5. Requirements touched this stage trace to `AMAZON FBA.pdf`
6. The handover note in `handover/` is sufficient for a stranger to continue
   (Unknown-Developer-Test, inherited)

# Failure / Blocker Behaviour

On a blocker: **stop at the gate. Do not work around it.**

Report `BLOCKED — <specific reason>`, and state: what is blocked, exactly what
fact or decision would unblock it, who owns that decision, and what was
completed before the block. Finish every independent part of the stage first;
only the dependent part waits.

Never substitute a guess to keep moving. A guess that reaches the dashboard is
worse than a stalled stage.

# Handoff to Next Stage

Write to `handover/`: stage completed, outcome, evidence paths, open questions
with owners, and the next stage's entry conditions. Then **stop** and let the
user open the next stage.
