# workflows/

## Purpose
The ordered process definitions this project runs on: the stage gates and the
repeatable operational routines.

## What belongs here
- The master workflow definition:
  New Task -> Folder Structure -> SKILL -> Discovery -> Analysis ->
  Build Features -> Skill Validation -> Fix -> Final Validation -> Task Complete
- Stage entry and exit criteria, and the one-stage-at-a-time rule
- Operational runbooks (e.g. how a weekly FBA refresh should be performed)
- Boundary rules that apply across stages, such as never writing to LEDSone

## What does NOT belong here
- Results of running a workflow (use `evidence/`, `validation/`, `closure/`)
- SQL or application code
- Prompt bodies (use `prompts/`)
- Ad-hoc notes with no repeatable process behind them
