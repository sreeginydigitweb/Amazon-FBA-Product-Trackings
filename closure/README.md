# closure/

## Purpose
Records that a stage or the whole task is finished and accepted. Closure is
written only after validation has passed.

## What belongs here
- Stage completion records with date and outcome (PASS / BLOCKED)
- Final task closure summary and acceptance notes
- Deliverables list with exact paths
- Residual risks and anything deliberately left out of scope
- Sign-off confirmations

## What does NOT belong here
- Work in progress (use `handover/`)
- Test evidence or validation runs (use `validation/` and `evidence/`)
- Anything that closes a stage whose validation has not actually passed
- New requirements. Those start a new task, not a closure entry
