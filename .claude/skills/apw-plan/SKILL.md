---
description: Structured planning workflow. Use when a task must be turned into a clear, checkable plan — optionally with requirements and design docs — before implementation begins.
disable-model-invocation: false
metadata:
  version: "1.0"
---

Use this skill when a task needs to be turned into a plan *before* anyone starts building it. It is the upstream companion to `apw-implement` and the downstream consumer of `apw-research` / `apw-brainstorm`: it takes a fuzzy brief (plus any docs or prior artifacts) and produces durable planning documents an implementation can execute against. It exists to fight two failures: jumping into code with the whole plan held only in volatile context — so steps get skipped and the work drifts from the original ask — and producing a plan that loses the thread of the requirements it was supposed to satisfy. Follow the steps below strictly and in order.

## Step 1 — Absorb the inputs

Before forming any plan, read everything that defines the task:

- **The brief** — the task description, ticket, or request as given.
- **Provided material** — any docs, specs, designs, or references handed in with it.
- **Prior artifacts** — if `docs/<task-short-slug>/research.md` or `brainstorm.md` exist, the plan **builds on them**, it does not redo their work.
- **The codebase** — the files, modules, and conventions the work will touch, so the plan fits reality instead of an imagined architecture.

Do not skip this step, even if the task looks small. A plan is only as good as the picture behind it.

## Step 2 — State your understanding, ask, and choose depth

First, write a **concise statement of what you understand needs to be done** — the goal, the scope as you read it, and the main pieces of work. This gives the user something concrete to correct before any document is written.

Then, in the same round:

- **Ask up to 5 main questions** — only genuine ambiguities that would change the plan. Skip any you can already answer from Step 1. Do not ask for the sake of asking.
- **Ask which document depth to produce** (use the AskUserQuestion tool):
  - **FULL** — `requirements.md` + `design.md` + `plan.md`. For larger or higher-stakes work where both the *what* and the *how* deserve to be pinned down before any code.
  - **MEDIUM** — `design.md` + `plan.md`. When the requirements are already clear (or captured upstream) but the technical/UI approach still needs deciding.
  - **PLAN** — `plan.md` only. For well-understood work that just needs to be broken into ordered, checkable steps.

Wait for the user's answers **and** their depth choice before producing anything.

## Step 3 — Produce and confirm the artifacts, one at a time

Write to `docs/<task-short-slug>/` in the project root, where `<task-short-slug>` is a short kebab-case name for the task (reuse the slug of any related research/brainstorm so the documents sit together).

**Produce the documents one by one, in dependency order — never all at once.** After writing each artifact, stop: tell the user it's ready (with its path) and a one-to-two-line summary of what it contains, then **wait for their review**. The user either:

- **approves** → move on to the next artifact, or
- **asks for changes** → revise *that* artifact and re-present it, repeating until they approve.

Only build the next artifact on a **confirmed** previous one — the design must not be written until requirements are approved, and the plan must not be written until the design (and requirements) are approved. This ordering is the point: later documents reference the IDs in earlier ones, so each gate prevents the next document from being built on something the user was about to change.

The sequence depends on the depth chosen in Step 2:

- **FULL** → `requirements.md` → *review* → `design.md` → *review* → `plan.md` → *review*
- **MEDIUM** → `design.md` → *review* → `plan.md` → *review*
- **PLAN** → `plan.md` → *review*

Each document, using the templates in this skill folder:

- **`requirements.md`** *(FULL only)* — `REQUIREMENTS_TEMPLATE.md`. Give every requirement a **stable ID** (R1, R2, …) so the design and plan can reference it. IDs must not change once assigned.
- **`design.md`** *(FULL and MEDIUM)* — `DESIGN_TEMPLATE.md`. Covers **technical design** (the main tech decisions, how the pieces fit, what was rejected) and — *only when the task has a user-facing surface* — **UI design** as **ASCII mockups**. Give every design decision a stable ID (D1, D2, …) and tie it to the requirement(s) it serves. Delete the UI section when there is no UI.
- **`plan.md`** *(all depths)* — `PLAN_TEMPLATE.md`. The ordered list of sub-tasks. Rules:
  - **Every sub-task is a checkbox: `- [ ]`**, left **unchecked**. The box is for the *implementing* agent to tick (`- [x]`) as it completes that task — it is the hand-off mechanism to `apw-implement`.
  - In **FULL/MEDIUM**, every task names what it implements — `(implements: R2, D1)`. A plan that doesn't trace back to the requirements/design it came from is the exact drift this skill exists to prevent. In **PLAN** depth there are no upstream docs, so drop the references and keep each task self-contained.
  - Each task is **concrete and independently checkable**, with a "done when" acceptance note.
  - **Order tasks so dependencies come first**; group into phases if the work is large.

## Step 4 — Surface conflicts

If, while planning, you find that the inputs contradict each other, a requirement is impossible as stated, or the scope is materially larger than the brief implied — **stop and tell the user**. Re-frame with them rather than writing a confident, well-structured plan for the wrong problem.

## Step 5 — Concise summary

Each artifact was already reviewed and approved one by one in Step 3, so this is a short final wrap, not the first chance to object. Keep the chat brief — the detail lives in the artifacts. Report only:

- The **depth chosen** and the **paths** to all documents written.
- The **shape of the plan** in a line or two — phase count, task count, the spine of the approach.

Then confirm it's ready to hand to `apw-implement`, and offer one last look in case anything across the set should change now that it's all visible together.
