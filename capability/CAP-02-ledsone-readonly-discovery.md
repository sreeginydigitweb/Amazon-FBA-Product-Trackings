# Name

CAP-02 — LEDSone Read-Only Discovery Guard

# Purpose

Make LEDSone inspection safe. LEDSone is the live source database for this
project and is **READ / SELECT ONLY**. The MCP tooling available does not
enforce that: `execute_sql` is a generic statement runner and will happily run a
write. The read-only boundary therefore has to be enforced by this capability,
not by the tool.

This capability also fences a second risk: the available text-to-SQL skills
(`text-to-sql-single`, `text-to-sql-multi`, `ppc-stock-lookup`) instruct
"mandatory postgres execution". That instruction is **subordinate** to this
file's boundary and to CAP-01's stage gate.

# Responsibility

One responsibility: **govern safe, read-only inspection of LEDSone.**

It does not decide what the data means (CAP-03) or what to do with it (CAP-04).

# Inputs

- A verified connection target (which connector, which database) — see
  MUST VERIFY below
- Schema metadata: catalog / `information_schema`, table and column definitions
- The field list CAP-03 needs resolved
- Reused schema knowledge from `text-to-sql-single`, `text-to-sql-multi`,
  `ppc-stock-lookup` (table routing and join paths — reuse, do not re-derive)

# Outputs

- A verified connection record in `capability/` (which database, access level,
  how it was confirmed)
- Schema inventory: tables, columns, types, nullability — written to `evidence/`
- Row-shape samples large enough to understand a column, no larger
- Candidate field mappings handed to CAP-03, each marked VERIFIED or UNVERIFIED
- A written NOT FOUND record for any required field with no source

# Allowed Actions

- Confirm **which** database is actually connected, before reading anything
- Read catalog / `information_schema` for schemas, tables, columns, types
- `SELECT` with an explicit small `LIMIT` for shape and value inspection
- `COUNT`, `MIN`, `MAX`, `DISTINCT` for cardinality and range diagnostics
- Read table definitions via the PostgreSQL MCP definition tools
- Record every query run, and its result, into `evidence/`

# Prohibited Actions

Against LEDSone, without exception:

- `INSERT`, `UPDATE`, `DELETE`, `MERGE`, `UPSERT`, `TRUNCATE`
- `DROP`, `ALTER`, `CREATE` (including temp tables, views, indexes)
- `GRANT`, `REVOKE`, `SET` of session state that changes behaviour
- Any statement inside a transaction intended to mutate, committed or not
- Any DDL "just to test"

Also prohibited:

- Querying before the connection target has been verified
- Naming a table or column that has not been observed in the schema
- Broad `SELECT *` with no `LIMIT` against a large table
- Assuming which connector is LEDSone (see MUST VERIFY)
- Exposing, echoing, logging, or storing credentials, tokens, or connection
  strings anywhere in the project — including in `evidence/`
- Opening a connection during the SKILL stage at all

# Source Boundaries

**MUST VERIFY — do not assume:**

Two SQL-capable connectors are present in this environment. Both expose a
write-capable `execute_sql`:

| Connector | Tools seen | Is it LEDSone? |
|---|---|---|
| `Ledsone_postgres` | `execute_sql`, `search_objects` | **UNVERIFIED** — name suggests yes, not proven |
| `postgres_2` | `execute_sql`, `search_objects`, `get_table_definition`, `list_table_definitions`, `analyze_db_health`, `list_skills`, `get_skill` | **UNVERIFIED** — hosts the text-to-SQL skills; target database unconfirmed |

`new-joiner-guide` Reference C records LEDSone as `varmen_db`, reached through
the `LEDSone MCP` connector, read access. That is org documentation, not a
verified fact about this session. **Discovery must confirm which connector
reaches LEDSone before any query.** Writing to the wrong database because the
connector was assumed is the worst outcome this capability exists to prevent.

The existing text-to-SQL skills describe tables (`ppc_performance`,
`listing_data`, `location_wise_inv_stock`, and others). Whether those tables
live in LEDSone is **UNVERIFIED**. Reuse them as candidate routing hints, then
confirm each table and column actually exists before relying on it.

# Business/Data Rules

- **Verify, then read.** Confirm the database, then the schema, then the table,
  then the column. Never skip a level.
- **Never guess a table or column name.** If it was not observed, it does not
  exist for this project.
- **Minimal diagnostic queries.** Inspect the smallest thing that answers the
  question. Do not pull production volumes to "have the data".
- **Platform scope: Amazon only.** Any channel discriminator must be verified,
  not assumed. The text-to-SQL skills suggest `which_channel=1` for Amazon —
  treat that as a hint to confirm, not a fact.
- **Product scope: "PH holder Dilani" only.** How this maps to LEDSone is
  **UNKNOWN and MUST BE VERIFIED in Discovery.** Defining a column for it by
  assumption is explicitly prohibited. See CAP-03.
- **No hard-coded reporting week or date.** Discovery must find the real date
  field and the real rolling-window semantics. A fixed week or a literal "today"
  baked into logic is a defect, not a shortcut.
- Record a **NOT FOUND** for a required field with no source. Do not improvise
  a substitute column that looks close.

# Validation Rules

1. Connection target verified and recorded before any query ran
2. Every statement executed was read-only — re-read the log to confirm
3. Every table and column referenced was observed in the schema first
4. Amazon-only scoping is based on a verified discriminator
5. The "PH holder Dilani" mapping is either VERIFIED with evidence, or recorded
   as still UNVERIFIED — never quietly assumed
6. No date or reporting week is hard-coded in anything produced
7. No credential appears anywhere in the project
8. Every query and result is in `evidence/` with its date

# Failure / Blocker Behaviour

- Cannot confirm which connector is LEDSone → **BLOCKED**. Do not query either.
- A required field has no verified source → record **NOT FOUND**, continue with
  the rest, and report the gap. Do not invent the field.
- The scope mapping cannot be resolved from the schema → **BLOCKED** on that
  scope, with the specific question that needs answering and who owns it.
- Any write is attempted or occurs → stop immediately, record exactly what ran
  in `evidence/`, and escalate. Do not attempt a corrective write.
- Read-only access is refused by the server → report it as a capability gap in
  `capability/`; do not seek a workaround credential.

# Handoff to Next Stage

Hand CAP-03 a schema inventory and a candidate field list where every entry is
VERIFIED, UNVERIFIED, or NOT FOUND — with evidence paths. Anything short of that
is not ready for Analysis.
