# prompts/

## Purpose
Reusable prompt text for this project, versioned so the same instruction can be
re-run and produce comparable results.

## What belongs here
- Stage prompts, one file per stage
- Discovery, Analysis, and Build prompt templates with placeholders
- Validation and fix prompts
- Notes on why a prompt is worded a certain way, and its known failure modes

## What does NOT belong here
- Prompt outputs or generated artefacts (use `evidence/` or `documentation/`)
- SKILL definitions. Those are SKILL files, authored in the SKILL stage
- Live secrets or real customer data inside example text
- Prompts that bake in a single reporting week or guessed table names
