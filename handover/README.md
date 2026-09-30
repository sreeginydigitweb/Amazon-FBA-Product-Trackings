# handover/

## Purpose
Everything needed for another person or another session to pick this project up
mid-flight without re-deriving context.

## What belongs here
- Handover notes per stage: what is done, what is in progress, what is next
- Current stage marker and the position in the workflow
- Preserved project constraints so they survive a context loss:
  - Source database: LEDSone, READ / SELECT ONLY
  - Platform scope: Amazon only
  - Product scope: all products belonging to / associated with "PH holder Dilani"
  - No hard-coded reporting week; week and date values stay parameterised
  - Final deliverable is a standalone `index.html`
  - UI must follow AMAZON FBA.pdf, especially the page 6 reference dashboard
- Pointers to the files that matter, with exact paths
- Blockers, and the exact question that needs answering

## What does NOT belong here
- Final sign-off (use `closure/`)
- Test results (use `validation/`)
- Long-form reference documentation (use `documentation/`)
- Duplicated copies of source files. Point to them by path instead
