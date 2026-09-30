# duplicate-risk-reports/

## Purpose
Records of where duplication has been detected or is likely, in the project
structure, in the deliverables, and in the data itself, so the same thing is not
built or counted twice.

## What belongs here
- Structure duplication findings: near-identical folders, files, or documents
- Deliverable duplication: competing UI files, e.g. `FBA Selection Tracking 2.html`
  and copies such as `FBA Selection Tracking 2 (1).html`, versus a later
  standalone `index.html` build
- Data duplication risk: repeated SKUs or ASINs, rows repeated across weeks, and
  double-counting risk in aggregates
- For each finding: what is duplicated, the risk, the decision taken, the owner

## What does NOT belong here
- The duplicated files themselves. Reference them by path instead
- Deletions performed without a recorded decision
- General bugs unrelated to duplication (use `validation/`)
- Data extracts (use `evidence/`)
