# capability/

## Purpose
A record of what this project and its tooling can and cannot actually do:
proven access, proven permissions, and proven limits. It also holds this
project's capability / SKILL definitions (`CAP-*.md`), which govern how the
later stages are allowed to operate.

## What belongs here
- Project capability / SKILL definitions (`CAP-01` … `CAP-05` below)
- Confirmed connector / database access and the access level held
  (LEDSone: read-only, recorded as a verified capability, not an assumption)
- Tooling available, and its limitations
- Capability gaps that block a stage, and what would unblock them
- Permission boundaries, e.g. no write access, no deploy rights

## What does NOT belong here
- Feature specifications or wish lists (use `documentation/`)
- Credentials, tokens, connection strings, or passwords. Never store secrets here
- Claimed capability that has not been verified
- Test results (use `validation/`)

---

# Capability / SKILL Index

Added in the SKILL stage, 2026-09-30. Each capability has **one** responsibility
and defers to the reused skills below rather than restating them.

| ID | Capability | CREATE / REUSE | Responsibility | Used by stage | Input | Output | Must NEVER do |
|---|---|---|---|---|---|---|---|
| **CAP-01** | [FBA Project Governance](CAP-01-fba-project-governance.md) | **CREATE** | Enforce stage order and FACT vs ASSUMPTION discipline | All stages | Declared stage, `handover/`, `validation/`, `AMAZON FBA.pdf` | Gate decision, stage record, handover note | Auto-advance a stage; record PASS for a check that was not run; commit or push |
| **CAP-02** | [LEDSone Read-Only Discovery Guard](CAP-02-ledsone-readonly-discovery.md) | **CREATE** | Govern safe, read-only inspection of LEDSone | Discovery | Verified connection target, schema metadata | Verified connection record, schema inventory, candidate fields | Any write to LEDSone; query before verifying the connector; guess a table or column; expose a credential |
| **CAP-03** | [FBA Source / Field Mapping](CAP-03-fba-source-field-mapping.md) | **CREATE** | Produce a verified field map for the 19 requirement fields | Discovery → Analysis | CAP-02 schema inventory, `AMAZON FBA.pdf` | `data-maps/` field map with a status per field, gap list | Invent a column; assume the "PH holder Dilani" mapping; treat prototype samples as source data |
| **CAP-04** | [FBA Rule Evaluation Governance](CAP-04-fba-rule-evaluation.md) | **CREATE** | Define how rules are evaluated and outcomes justified | Analysis → Build | CAP-03 verified map, PDF rules | Per-ASIN outcome + reason, notification list | Evaluate on unverified input; default a missing value; change a threshold to qualify more products; build the engine during SKILL |
| **CAP-05** | [Standalone Dashboard Governance](CAP-05-fba-static-dashboard.md) | **CREATE** | Protect UI fidelity and data honesty in `index.html` | Build → Validation | `AMAZON FBA.pdf` p6, prototype HTML, CAP-04 outcomes | Standalone `index.html`, fidelity check, render evidence | Redesign the supplied UI; ship prototype sample rows; create `index.html` before Build |

## Reused — do not duplicate

Existing skills and connectors this project relies on. **None of these were
copied into the project.** Reference them in place.

| Reused asset | Location | Role in this project |
|---|---|---|
| `new-joiner-guide` | Synced skill | **Parent governance.** Owns the 12-folder standard, GREEN/AMBER/RED tiers, Evidence Rule, Existing-Asset-First, Unknown-Developer-Test, escalation owners, and the Our Databases reference. CAP-01 defers to it |
| `text-to-sql-single` | `postgres_2` MCP skill | Single-domain table routing: orders, stock, returns, listings, fees, thresholds |
| `text-to-sql-multi` | `postgres_2` MCP skill | Multi-domain chained-CTE join paths, channel consistency, aggregate-before-join |
| `ppc-stock-lookup` | `postgres_2` MCP skill | ASIN → SKU → inventory bridge via `listing_data`, wrong-SKU check, `mapped_sku` fallback, clean-SKU step |
| `pdf` | Synced skill | Reading `AMAZON FBA.pdf`, the canonical requirement |
| `build-dashboard` | Synced skill | Dashboard craft: ground every number, filter correctness, copy discipline |
| `dataviz` | Built-in skill | Chart types, accessible palette, KPI tiles |
| `artifact-design` | Built-in skill | Design calibration |
| `browser-automation` | Synced skill | Verifying the built `index.html` renders without console errors |
| `skill-builder`, `skill-creator` | Synced skills | Used to author the CAP files in this stage |
| `Ledsone_postgres`, `postgres_2` | MCP connectors | Read access path. **Which one reaches LEDSone is UNVERIFIED** — CAP-02 must confirm before any query |

## Convention note

Mini-AIOS convention reserves `prompts/SKILL.md` for a **GPT-authored**,
cut-down per-task governance file used as a GPT project source. That file is
**intentionally not created here** — authoring it in this stage would duplicate
the CAP files above. Project capability definitions live in `capability/`, per
the SKILL-stage instruction. No competing folder structure was introduced.

## Scope constraints binding on every capability

- **Platform: Amazon only.**
- **Products: only those belonging to / associated with "PH holder Dilani"** —
  the mapping is **UNKNOWN and MUST BE VERIFIED in Discovery.** Guessing it is
  prohibited in CAP-02 and CAP-03.
- **LEDSone is READ / SELECT ONLY.** No INSERT, UPDATE, DELETE, ALTER, DROP,
  CREATE, TRUNCATE, GRANT.
- **No hard-coded reporting week, current date as business logic, or static
  30-day sales value.**
- **Reference priority:** `AMAZON FBA.pdf` > verified LEDSone data >
  `FBA Selection Tracking 2.html` (layout only; its sample rows are never data).
