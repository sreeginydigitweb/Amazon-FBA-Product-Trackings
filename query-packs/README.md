# query-packs/

## Purpose
Curated bundles of related, SELECT-only queries that run together to answer one
question end to end. The reusable counterpart to the single files in `sql/`.

## What belongs here
- Named packs, one folder per pack, e.g. `query-packs/fba-product-scope/`
- A manifest per pack: what it answers, run order, parameters, expected shape
- Parameterised inputs (week, date, scope filters), never a fixed week
- Read-only queries only, consistent with LEDSone being SELECT-only

## What does NOT belong here
- Any statement that writes to, alters, or creates objects in LEDSone
- Packs built on unverified tables or columns
- Pack run output (use `evidence/`)
- Single unrelated ad-hoc queries (use `sql/`)
