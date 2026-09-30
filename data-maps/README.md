# data-maps/

## Purpose
The verified bridge between business language and the real LEDSone data model.
Nothing here may be guessed. Every mapping must be traceable to Discovery
evidence.

## What belongs here
- Business term to table to column mappings, each with an evidence reference
- The verified resolution of "PH holder Dilani" to concrete LEDSone entities.
  This MUST come from Discovery; it is never to be assumed
- Amazon-platform scoping rules: how an Amazon-only product set is identified
- Join paths between entities, and the keys used
- Field-level notes: type, nullability, units, date semantics
- Explicitly flagged UNVERIFIED entries, kept separate from verified ones

## What does NOT belong here
- Guessed or inferred table and column names presented as confirmed
- Executable SQL (use `sql/` or `query-packs/`)
- Extracted data rows (use `evidence/`)
- Business rules and calculation logic (use `documentation/`)
