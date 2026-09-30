# Name

CAP-05 — Standalone FBA Dashboard (`index.html`) Governance

# Purpose

Govern the later build of the required standalone `index.html`, so it stays
faithful to the supplied reference UI and never displays a number that the
verified data does not support.

**`index.html` must NOT be created during the SKILL stage.**

# Responsibility

One responsibility: **protect UI fidelity and data honesty in the deliverable.**

It does not source, map, or evaluate anything (CAP-02 / CAP-03 / CAP-04).

This capability is deliberately **thin**. General dashboard craft is already
covered by existing skills and is **reused, not restated**:

| Reused skill | What it already covers |
|---|---|
| `build-dashboard` | Grounding every number in real data, filter correctness across all views, copy discipline, verify-before-deliver |
| `dataviz` | Chart type selection, accessible light/dark palette, KPI tiles, tooltips |
| `artifact-design` | Design investment calibration |
| `browser-automation` | Loading the built page and reporting console errors and render failures |

CAP-05 adds only the four things those skills do not know about: the supplied
reference UI, the standalone-single-file constraint, the prototype-is-not-data
rule, and the required column and control set.

# Inputs

- `AMAZON FBA.pdf` — reference dashboard, **especially page 6** (canonical layout)
- `FBA Selection Tracking 2.html` — prototype UI reference (structure only)
- CAP-04's per-ASIN outcome set, including `INSUFFICIENT DATA` counts
- CAP-03's verified field map, for column provenance

# Outputs

- A single standalone `index.html` at the project root
- A UI fidelity check against `AMAZON FBA.pdf` page 6, recorded in `validation/`
- Render evidence (screenshot, console output) in `evidence/`

# Allowed Actions

- Reproduce the supplied reference layout, columns, chips, and controls
- Inline all CSS and JS so the file opens standalone
- Show `INSUFFICIENT DATA` and coverage gaps as visible states
- Reuse the skills listed above for chart, colour, and verification craft
- Ask when the PDF and the prototype HTML disagree on layout

# Prohibited Actions

- Building `index.html` during the SKILL stage
- **Redesigning the supplied UI.** No re-theming, re-laying-out, or "improving"
  what the reference already settles
- Shipping any value not traceable to CAP-04's output
- **Carrying over the prototype's hard-coded sample products** into the
  deliverable, as data, placeholder, or fallback
- Filling an empty state with plausible-looking rows
- External runtime dependencies that break the file when opened offline
- Hard-coding a reporting week, or a "current date" that is really business logic
- Displaying out-of-scope products (non-Amazon, or outside "PH holder Dilani")
- Firing a real notification from the page
- Deploying, committing, or pushing the file

# Source Boundaries

Layout authority: `AMAZON FBA.pdf` page 6 first; `FBA Selection Tracking 2.html`
second, for structure only. **On conflict, the PDF wins.**

Data authority: CAP-04 outcomes built on CAP-03's verified map. Nothing else.

**The prototype's sample products are not production data.** They exist to show
shape. If they reach the deliverable, the deliverable is wrong — and the
Downloads copies of the prototype are not working references either
(`duplicate-risk-reports/DRR-01-canonical-references.md`).

# Business/Data Rules

Must be preserved from the supplied reference:

- **Required columns** — exactly the set the reference shows, in its order.
  Do not add a column because it is available, or drop one because it is empty.
- **Filters and search** — every control must filter **every** view on the page
  consistently. A control that updates one region and silently leaves another
  unfiltered is a correctness bug.
- **Status chips** — one visual state per CAP-04 outcome: `No Action`,
  `Review Stock`, `Not Eligible / Do Not Select`, `FBA Eligible`, and
  `INSUFFICIENT DATA`. Do not collapse `INSUFFICIENT DATA` into a fail.
- **Rule indicators** — show which criterion drove the outcome, per row, so a
  decision is readable without re-running anything.
- **Actions** — as the reference defines them. A notification action must be
  inert until the notification requirement is verified and approved.
- **Responsive behaviour** — usable at small widths; wide tables scroll inside
  their own container rather than forcing the page to scroll sideways.
- **Standalone** — one file, no build step, no network fetch, opens from disk.
- **Dynamic dates** — the reporting week and the 30-day window derive from the
  data at render time. Never a baked-in week.
- **Source correctness** — every column traces to a VERIFIED entry in
  `data-maps/`. A column with no verified source does not ship.

# Validation Rules

1. Layout compared against `AMAZON FBA.pdf` page 6; differences recorded and
   justified
2. Required columns present, correctly ordered, correctly labelled
3. Every filter and the search box affect every view — each one clicked and
   confirmed
4. A chip exists for each CAP-04 outcome, `INSUFFICIENT DATA` included
5. Rule indicators match CAP-04's recorded deciding rule, spot-checked per row
6. Zero prototype sample products present — grep the file to prove it
7. Every displayed value traces to CAP-04 output; headline figures recomputed a
   second way and matched
8. File opens standalone with no network access; no external runtime dependency
9. No hard-coded week or business-logic date
10. Only Amazon, only "PH holder Dilani" scope on screen
11. Responsive at small widths; no horizontal page scroll
12. Page loads with no console errors (verified via `browser-automation`)
13. No notification sent during validation

# Failure / Blocker Behaviour

- PDF and prototype conflict on layout → follow the PDF, record the difference,
  and flag it. Do not invent a third option.
- A required column has no VERIFIED source → **BLOCKED** on that column. Build
  the rest, show the column as unavailable, and report it. Do not fill it.
- CAP-04 output is unavailable or partly `INSUFFICIENT DATA` → build the page
  and show the gap honestly. Never a sample row, never a plausible estimate.
- Page will not render standalone → **BLOCKED**. An `index.html` that needs a
  server does not meet the requirement.

# Handoff to Next Stage

Hand Skill Validation the built `index.html`, the fidelity comparison in
`validation/`, and render evidence in `evidence/`. Any BLOCKED column or
unresolved conflict is listed explicitly — not left for a reviewer to notice.
