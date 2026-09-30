# documentation/

## Purpose
The explanatory layer of the project: what was decided, why, and how the system
is intended to work. This is the place a new joiner reads first.

## What belongs here
- Stage write-ups (Folder Structure, Discovery, Analysis, Build, Validation)
- Data dictionary / column meanings once verified in Discovery
- The verified mapping of "PH holder Dilani" to real LEDSone entities, with a
  pointer to the evidence that proves it
- Business rules for FBA product selection and tracking, in prose
- Architecture notes, assumptions, open questions, and known limitations
- Constraint records: LEDSone is READ / SELECT ONLY; platform scope is Amazon only

## What does NOT belong here
- Raw query output or screenshots (use `evidence/`)
- Executable SQL (use `sql/`)
- Prompt text (use `prompts/`)
- Handover packs (use `handover/`) or sign-off records (use `closure/`)
- Guesses presented as fact. Anything unverified must be labelled ASSUMPTION
