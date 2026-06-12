# Requirements Template

Copy this structure into your work item's **`requirements.md`** artifact — see `apw-work-with-work-item-artifacts` for where work-item artifacts live. Give every
requirement a stable ID (R1, R2, …) — the design and plan reference these IDs,
so they must not change once assigned. State each requirement as a single,
testable claim: "the system must …", not "it would be nice if …".

```markdown
# Requirements: <task>

- **Date:** <YYYY-MM-DD>
- **Slug:** <task-short-slug>
- **Status:** draft | confirmed

## Context

Why this work exists, in a few sentences — the problem and who it's for. Link
any upstream `research.md` / `brainstorm.md` this builds on.

## Goals

- What this work must achieve.

## Non-goals

- What is explicitly out of scope, so the plan doesn't sprawl.

## Requirements

Each requirement is a single, testable statement with a stable ID and a
priority.

| ID | Requirement | Priority |
|----|-------------|----------|
| R1 | ... | must |
| R2 | ... | must |
| R3 | ... | should |

> Priority: **must** (without it the work fails), **should** (important, not
> blocking), **could** (nice to have).

## Acceptance criteria

The observable conditions that define "done" for this work as a whole — how
anyone can tell the requirements are met.

- ...

## Open questions

Anything unresolved that affects what is required.

- ...
```
