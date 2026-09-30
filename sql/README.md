# sql/

## Purpose
Individual SQL files for this project. LEDSone is the source database and is
READ / SELECT ONLY, so every file here must be non-mutating.

## What belongs here
- SELECT-only exploration and reporting queries, one concern per file
- Schema inspection queries (information_schema / catalog reads)
- Queries parameterised by week or date, so no single reporting week is baked in
- A header comment in each file: purpose, source tables, parameters, author, date

## What does NOT belong here
- Any INSERT, UPDATE, DELETE, MERGE, TRUNCATE, DROP, ALTER, CREATE or GRANT
  against LEDSone. Writes to LEDSone are forbidden in this project
- Hard-coded single reporting weeks or one-off literal dates as the only input
- Queries referencing tables or columns not yet verified in Discovery
- Curated multi-query bundles (use `query-packs/`)
- Query output (use `evidence/`)
