# Name

CAP-03 — FBA Source / Business Field Mapping

# Purpose

Bind each field the FBA requirement needs to a real, observed LEDSone column —
or to an explicit **UNKNOWN / NOT FOUND / UNVERIFIED**. This is the capability
that decides what the data *means*, and it is where invented data would enter
the system if it entered at all.

# Responsibility

One responsibility: **produce a verified field map.**

It does not query (CAP-02 does), does not apply thresholds (CAP-04), does not
render (CAP-05).

# Inputs

- CAP-02's schema inventory and evidence
- The FBA requirement fields listed below, from `AMAZON FBA.pdf` (canonical)
- Candidate routing hints reused from `text-to-sql-single`,
  `text-to-sql-multi`, `ppc-stock-lookup` — hints only, each to be confirmed

# Outputs

- `data-maps/` — the field map: requirement field -> table -> column -> evidence
  path, with a status per field
- Join paths and keys, each observed rather than inferred
- A gap list of NOT FOUND fields, with what would be needed to resolve each
- Field semantics: units, type, nullability, date meaning

Every row carries exactly one status:

| Status | Meaning |
|---|---|
| **VERIFIED** | Column observed; values inspected; meaning confirmed by evidence |
| **UNVERIFIED** | Candidate column identified; meaning not yet confirmed |
| **UNKNOWN** | Cannot yet tell how the requirement maps to the schema |
| **NOT FOUND** | No column in LEDSone supplies this field |

# Allowed Actions

- Map a requirement field to a column **only** after CAP-02 observed it
- Record several candidate columns for one field, all marked UNVERIFIED
- Record a field as NOT FOUND and continue mapping the rest
- Reuse documented join paths from the existing text-to-SQL skills, after
  confirming each table and key exists
- Flag two fields that resolve to the same column as a duplication risk

# Prohibited Actions

- **Inventing a column, table, or value** for a field with no source
- Substituting a "close enough" column without recording the substitution and
  its risk
- Presenting UNVERIFIED as VERIFIED, or dropping a status once assigned
- **Defining any column as the "PH holder Dilani" field by assumption**
- Assuming the Amazon channel discriminator
- Deriving `net margin` from a formula not traceable to `AMAZON FBA.pdf`
- Treating sample rows in `FBA Selection Tracking 2.html` as source data, or as
  proof that a field exists
- Baking a fixed week, fixed date, or static 30-day sales figure into the map

# Source Boundaries

Reference priority: `AMAZON FBA.pdf` > verified LEDSone data >
`FBA Selection Tracking 2.html` (layout only).

**Scope constraints that must survive into every mapping:**

- **Platform: Amazon only.** Not eBay, not Shopify, not any other channel. The
  discriminator must be verified.
- **Products: only those belonging to / associated with "PH holder Dilani".**

**"PH holder Dilani" — MUST VERIFY, NEVER GUESS.**

There is no approved mapping for this. It is not known whether it is a holder
table, an owner or account column, a listing attribute, a supplier or brand
reference, a naming convention, or something else. Discovery must establish it
from the real schema and data, with evidence. Until then the field's status is
**UNKNOWN**, and no query, rule, or dashboard may depend on a guessed version of
it. If it cannot be resolved from the schema, that is a **BLOCKER** to be
escalated — not a gap to be filled with a plausible column.

# Business/Data Rules

Fields the requirement needs mapped. Every one starts as **UNKNOWN** and is
resolved only by evidence:

| # | Requirement field | Notes for mapping |
|---|---|---|
| 1 | Amazon platform | Channel discriminator — verify, don't assume |
| 2 | PH holder Dilani | **MUST VERIFY — never guess** |
| 3 | ASIN | Amazon identifier; confirm the ASIN -> SKU bridge |
| 4 | Product name | Confirm which of several name columns is authoritative |
| 5 | Current stock | Location/warehouse-scoped; confirm which location counts |
| 6 | Last 30-day sales | **Rolling window** — never a static number |
| 7 | Rating | Distinguish account-level from per-ASIN (see #17) |
| 8 | Net margin | Formula must trace to the PDF, not be invented |
| 9 | Size | Units must be recorded |
| 10 | Weight | Units must be recorded |
| 11 | Already-in-FBA status | Drives STEP 0 in CAP-04 |
| 12 | FBA history | Prior FBA participation, distinct from current status |
| 13 | Fragile / crystal | Exclusion input |
| 14 | Overweight / oversized | Exclusion input; thresholds from the PDF |
| 15 | Return history | Confirm channel scoping and time window |
| 16 | Damage history | May share a source with returns — check for overlap |
| 17 | Individual ASIN rating | Per-ASIN, explicitly separate from #7 |
| 18 | Exclusions | Which conditions exclude, per the PDF |
| 19 | Notification requirement | What triggers a notification, and to whom |

Rules on top of the table:

- **ASIN -> SKU resolution is a known hazard.** `ppc-stock-lookup` documents a
  bridge through `listing_data` with a wrong-SKU check, a `mapped_sku` fallback,
  and a clean-SKU step that strips listing-variant suffixes. Reuse that logic;
  do not write a new one. A resolved value can still be a listing SKU that will
  not match inventory — record that risk explicitly in the map.
- **One ASIN can map to many SKUs.** Note the double-counting exposure for any
  aggregate, and log it to `duplicate-risk-reports/`.
- **Aggregate before joining.** Inherited from the text-to-SQL skills; joining
  first inflates totals.
- **Dates stay dynamic.** Record the date field and the window semantics, never
  a resolved week.
- Record units, nulls, and coverage gaps honestly. A null is information; a
  zero-filled null is a fabrication.

# Validation Rules

1. Every one of the 19 fields has exactly one status, and no field is silently
   missing from the map
2. Every VERIFIED field cites an evidence path
3. No column name appears that CAP-02 did not observe
4. "PH holder Dilani" is VERIFIED with evidence, or explicitly UNKNOWN —
   never assumed
5. Amazon-only scoping rests on a verified discriminator
6. No fixed date, week, or static 30-day figure anywhere in the map
7. Per-ASIN rating and account-level rating are distinct entries
8. Net margin's formula traces to `AMAZON FBA.pdf`
9. Duplicate / double-count risks are logged to `duplicate-risk-reports/`

# Failure / Blocker Behaviour

- A field has no source → **NOT FOUND**. Map the rest. Report the gap with what
  would resolve it. Never fill it in.
- "PH holder Dilani" unresolvable from the schema → **BLOCKED**, escalated with
  the precise question and the owner. Do not proceed to a scoped query.
- Two plausible columns and no way to choose → record both as UNVERIFIED and
  ask. Do not pick the one that makes the dashboard work.
- A field is only available in the prototype HTML → that is **NOT FOUND** in
  LEDSone. Say so plainly.

# Handoff to Next Stage

Hand CAP-04 a field map in which every rule input is **VERIFIED**. A rule may
not be evaluated on an UNVERIFIED, UNKNOWN, or NOT FOUND input — CAP-04 must
refuse it.
