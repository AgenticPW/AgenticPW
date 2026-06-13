# Handoff Template

Copy this structure into your work item's **`handoff.md`** artifact — see
`apw-work-with-work-item-artifacts` for where work-item artifacts live. This is a
**living document**: it describes the *current* state of the task, so update or
overwrite it on every checkpoint instead of appending — history lives in version
control. Write for a reader with zero conversation history who cannot ask you
follow-up questions: prefer exact references (`path/to/file.ts:42`, commit SHAs,
commands) over prose.

```markdown
# Handoff: <the task in one line>

- **Date:** <YYYY-MM-DD>
- **Slug:** <task-short-slug>
- **Task status:** in progress | blocked | nearly done

## Objective

What the task is trying to achieve, in one or two sentences, and how success is
recognized. Link the sibling artifacts that carry the durable context
(`plan.md`, `root-cause.md`, `research.md`, …).

## State of play

### Done
What is complete — and whether it is verified or merely written.

- ...

### In flight
What is started but unfinished: half-edited files, a partial refactor, a test
that doesn't pass yet. Name the exact files and what state they are in.

- ...

### Next
The concrete next action — the first thing the resuming session should do, not
a vague direction.

1. ...

## Decisions made

Settled decisions *with their why*. A decision recorded without its reason will
be re-litigated.

| Decision | Why |
|----------|-----|
| ... | ... |

## Dead ends

Approaches already tried and abandoned, and why they failed — so the next
session doesn't reach for the same plausible ideas.

| Tried | Why it failed |
|-------|---------------|
| ... | ... |

## Open questions

Anything unresolved — especially questions waiting on the user.

- ...

## Pointers

The specific places and tools in play: files and line ranges being edited,
relevant commands (build, test, repro), branches, and anything else the next
session needs at hand.

- ...
```
