# evidence/

## Purpose
Raw, timestamped proof that a stage actually happened. Evidence is the audit
trail for the Amazon FBA Product Selection & Tracking project: if a claim is
made in documentation, validation, or closure, the backing artefact lives here.

## What belongs here
- Query result exports from Discovery (CSV / JSON / text) with the date captured
- Screenshots of the LEDSone schema, table lists, or row samples
- Console / terminal logs from read-only checks
- Screenshots of reference material (e.g. AMAZON FBA.pdf page 6 dashboard)
- Before/after captures used to justify a decision
- Subfolders per stage or per date, e.g. `evidence/discovery/2026-09-30/`

## What does NOT belong here
- Interpretation, conclusions, or analysis write-ups (use `documentation/`)
- Reusable SQL scripts (use `sql/` or `query-packs/`)
- Application code or `index.html`
- Anything edited after capture. Evidence is append-only; corrections go in a
  new file and the original is never rewritten
- Credentials, connection strings, or passwords
