# Plan Template

Copy this structure into your work item's **`plan.md`** artifact — see `apw-work-with-work-item-artifacts` for where work-item artifacts live. Every sub-task is a
`- [ ]` checkbox left **unchecked** — the implementing agent ticks it (`- [x]`)
as the task is completed, so the plan tracks real progress. In FULL/MEDIUM
depth, each task names the requirement/design IDs it implements, so the plan
always traces back to what it came from. In PLAN depth there are no upstream
docs, so drop the `(implements: …)` references and keep each task
self-contained.

```markdown
# Plan: <task>

- **Date:** <YYYY-MM-DD>
- **Slug:** <task-short-slug>
- **Depth:** full | medium | plan
- **Implements:** <links to requirements.md / design.md, if any>

## Approach

The spine of the plan in a few sentences — the order of attack and why.

## Tasks

Ordered so dependencies come first. Group into phases if the work is large.
Each box stays unchecked until the implementing agent completes that task.

### Phase 1 — <name>

- [ ] **T1 — <title>** — <what to do, concretely>. _(implements: R1, D1)_
  - Done when: <observable acceptance condition>
- [ ] **T2 — <title>** — <what to do>. _(implements: R2)_
  - Done when: <...>

### Phase 2 — <name>

- [ ] **T3 — <title>** — <what to do>. _(implements: D2)_
  - Done when: <...>

## Verification

How the finished work will be checked as a whole — tests to run, behaviors to
observe. Hands off to `/apw-verify`.

- ...

## Risks

- ...

## Open questions

- ...
```
