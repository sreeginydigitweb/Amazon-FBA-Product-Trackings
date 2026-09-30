# capability/

## Purpose
A record of what this project and its tooling can and cannot actually do:
proven access, proven permissions, and proven limits.

## What belongs here
- Confirmed connector / database access and the access level held
  (LEDSone: read-only, recorded as a verified capability, not an assumption)
- Tooling available, and its limitations
- Capability gaps that block a stage, and what would unblock them
- Permission boundaries, e.g. no write access, no deploy rights

## What does NOT belong here
- Feature specifications or wish lists (use `documentation/`)
- Credentials, tokens, connection strings, or passwords. Never store secrets here
- Claimed capability that has not been verified
- Test results (use `validation/`)
