[← All rationales](index.md)

# apw-plan

`/apw-plan "<the task to plan, or a path to its brief/docs>"`

A structured planning workflow: absorb → understand & choose depth → produce artifacts → surface conflicts → summarize & invite corrections. It is the upstream companion to [apw-implement](apw-implement.md) and the downstream consumer of [apw-research](apw-research.md) / [apw-brainstorm](apw-brainstorm.md) — it turns a fuzzy brief into durable planning documents an implementation can execute against.

## The bad practice it prevents

Asked to tackle anything bigger than a one-liner, the natural instinct is to start editing and keep the plan "in your head." For an agent, "in your head" is a context window that drifts.

**Without the skill:**

- **The plan is volatile.** It lives only in the conversation, so as context fills, steps get dropped and the agent forgets what it already decided.
- **Work drifts from the ask.** Without a written target, the implementation slowly becomes a slightly different thing than what was requested — and nobody notices until review.
- **No traceability.** Even when a plan exists, its tasks don't tie back to the requirements or design they came from, so it's impossible to tell whether the plan actually covers the requirement.
- **One-size planning.** Every task gets the same treatment — either over-documented when it didn't need it, or rushed into code when it deserved a design pass first.
- **No hand-off.** The plan and the implementation are disconnected; there's no shared checklist that shows what's done and what's left.

## How the skill helps

It replaces "keep it in your head" with five ordered checkpoints, and lets you choose how much documentation the task actually warrants.

1. **Absorb the inputs.** Read the brief, any provided docs, prior `research.md` / `brainstorm.md`, and the codebase the work will touch — so the plan fits reality, not an imagined architecture.
2. **Understand & choose depth.** State what you understand needs to be done, ask up to 5 questions that would change the plan, and pick the depth: **FULL** (requirements + design + plan), **MEDIUM** (design + plan), or **PLAN** (just the task list).
3. **Produce & confirm one at a time.** Write the documents in dependency order — requirements, then design, then plan — pausing after each for the user to approve or revise it before the next is started. Requirements get IDs (R1, R2…), design decisions get IDs (D1, D2…), and every plan task names what it implements.
4. **Surface conflicts.** If the inputs contradict each other or the scope is bigger than the brief implied, stop and re-frame with the user instead of planning the wrong problem confidently.
5. **Summarize.** A short final chat summary points to the approved artifacts and confirms they're ready to hand to implementation.

> **The core idea:** a plan is only useful if it survives the conversation and traces back to intent. This skill front-loads the cheap discipline — a written, ID-referenced, checkable plan at the depth the task warrants — so the work doesn't drift and the hand-off to implementation is a shared checklist, not a re-derivation.

## Right-sized by design

Not every task deserves a requirements doc, and not every task can skip a design pass. The depth choice makes that explicit:

| Depth | Produces | Use when |
|-------|----------|----------|
| **FULL** | `requirements.md` + `design.md` + `plan.md` | Larger or higher-stakes work where both the *what* and the *how* must be pinned down first. |
| **MEDIUM** | `design.md` + `plan.md` | Requirements are already clear, but the technical/UI approach needs deciding. |
| **PLAN** | `plan.md` | Well-understood work that just needs breaking into ordered, checkable steps. |

Two rules keep the output honest:

- **Everything traces back.** In FULL/MEDIUM, every plan task carries the requirement/design IDs it implements — `(implements: R2, D1)` — so a reader can confirm the plan covers the requirements instead of taking it on faith.
- **Design means both kinds.** `design.md` captures the **technical design** (the main tech decisions and what was rejected) and, when the task has a user-facing surface, the **UI design** as **ASCII mockups** — so the layout is concrete before it's built.

The artifacts use the templates in [`.claude/skills/apw-plan/`](../../.claude/skills/apw-plan/): [`REQUIREMENTS_TEMPLATE.md`](../../.claude/skills/apw-plan/REQUIREMENTS_TEMPLATE.md), [`DESIGN_TEMPLATE.md`](../../.claude/skills/apw-plan/DESIGN_TEMPLATE.md), and [`PLAN_TEMPLATE.md`](../../.claude/skills/apw-plan/PLAN_TEMPLATE.md).

## The checkbox hand-off

Every plan task is a `- [ ]` checkbox, left unchecked. That isn't decoration — it's the hand-off to [apw-implement](apw-implement.md), which reads the `plan.md` if one exists and ticks each box (`- [x]`) as it completes that task. The plan becomes a live record of progress that both skills share, so "what's left" is never a guess.

## Sample walkthrough

A developer needs to add CSV export to a reports page.

### 1 · The request

```
user: /apw-plan "add CSV export to the reports page"
```

### 2 · Understand, then choose depth

```
assistant: Understanding: add a button on /reports that exports the currently
filtered rows as CSV, server-side, respecting the user's column choices.

Questions:
  1. Should the export respect the active filters, or always export all rows?
  2. Streamed for large result sets, or a single response is fine?

This touches a new UI control and a new endpoint — depth?
  • FULL   — requirements + design + plan
  • MEDIUM — design + plan
  • PLAN   — just the task list
user: respects filters; single response is fine. MEDIUM.
```

### 3 · Produce each artifact, review, then the next

```
assistant: Wrote docs/csv-export/design.md.
D1 reuse the existing report query + a CSV serializer; D2 a new
GET /reports/export endpoint; UI mockup of the button + its loading state.
Review it — approve, or tell me what to change?
user: drop the loading state, it'll be instant. otherwise good.

assistant: Updated design.md (removed the loading state). Approved?
user: yes

assistant: Wrote docs/csv-export/plan.md — 6 tasks across 2 phases, each a
[ ] box tracing back to D1/D2. Review it?
user: looks right, ship it to implement
```

The "just add export" task became a MEDIUM-depth pair of documents — a design with an ASCII mockup, approved before the plan was even started, and a six-task checklist that traces to it — ready to hand to `/apw-implement`, which will tick the boxes as it builds.

---

Part of [AgenticPW](../../README.md) · See also the [apw-implement rationale](apw-implement.md) and [apw-research rationale](apw-research.md).
