# DRR-01 — Canonical Source References

**Date:** 2026-09-30
**Stage raised:** Folder Structure (detected) → SKILL (decision recorded)
**Type:** Duplicate source-reference risk
**Status:** DECIDED — documentation only, no files deleted

---

## Finding

Duplicate copies of both supplied source references exist outside the project.
All copies are currently byte-identical.

| File | Location | MD5 | Role |
|---|---|---|---|
| `AMAZON FBA.pdf` | **Project root** | `71bc2fc9e0778583a6957c4ed07f20b7` | **CANONICAL** |
| `AMAZON FBA.pdf` | `C:\Users\LED 309\Downloads\` | `71bc2fc9e0778583a6957c4ed07f20b7` | Duplicate — not a working reference |
| `FBA Selection Tracking 2.html` | **Project root** | `47c718753bccff31a3b228e8fad14417` | **CANONICAL** |
| `FBA Selection Tracking 2.html` | `C:\Users\LED 309\Downloads\` | `47c718753bccff31a3b228e8fad14417` | Duplicate — not a working reference |
| `FBA Selection Tracking 2 (1).html` | `C:\Users\LED 309\Downloads\` | `47c718753bccff31a3b228e8fad14417` | Duplicate — not a working reference |

## Risk

The copies are identical **today**. The risk is divergence: if a requirement is
revised in one copy only, a later stage could build against a stale requirement
without anyone noticing, because the filenames give no hint of which is current.
The `(1)` suffixed copy is the most likely to be opened by accident.

## Decision

The **project-root** copies are the canonical references for this task:

```
C:\Users\LED 309\OneDrive\Documents\Amazon FBA Product Tracking\AMAZON FBA.pdf
C:\Users\LED 309\OneDrive\Documents\Amazon FBA Product Tracking\FBA Selection Tracking 2.html
```

- Downloads copies **must not** be used as working references at any stage.
- External duplicates are **not deleted** — they are outside project scope and
  removing a user's files is not this project's call.
- If a Downloads copy is ever found to differ from the project root copy, that
  is a **BLOCKER**: stop and confirm with the requirement owner which is current.
  Do not assume the newer timestamp wins.

## Reference priority (unchanged by this report)

1. `AMAZON FBA.pdf` — canonical business requirement
2. Verified LEDSone source data — canonical data truth
3. `FBA Selection Tracking 2.html` — UI / layout reference **only**

The prototype HTML contains hard-coded sample products. They are **never**
production data and never evidence. See CAP-05.

## Related duplication risks — open, not yet assessed

| Risk | Raised in | Status |
|---|---|---|
| One ASIN mapping to many SKUs, causing double-counted aggregates | CAP-03 | OPEN — assess in Discovery |
| A later `index.html` coexisting with the prototype HTML as competing deliverables | CAP-05 | OPEN — assess in Build |
| Two requirement fields resolving to the same LEDSone column | CAP-03 | OPEN — assess in Discovery |

## Recorded by

SKILL stage, 2026-09-30. Cross-referenced from `capability/README.md`,
CAP-01, CAP-03, CAP-05.
