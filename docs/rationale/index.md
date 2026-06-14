# Why these skills exist

Each skill in this collection replaces a tempting shortcut with a small, repeatable discipline. These pages explain the bad practice each one prevents, how the skill helps, and what it looks like in action.

## Skills

### [`/apw-brainstorm`](apw-brainstorm.md) — Structured ideation

Re-frame the problem, diverge under multiple lenses, push past the safe first cluster, then converge to a ranked shortlist — so brainstorming beats the model's mode collapse instead of surrendering to it.

### [`/apw-research`](apw-research.md) — Structured research

Frame the question, gather referenced evidence, verify it, and record a recommendation — so research is trustworthy and never redone.

### [`/apw-tech-design`](apw-tech-design.md) — Technical design

Familiarize, ask if needed, present up to 3 technical options with pros/cons and a recommendation, confirm with the user, then write a compact `tech-design.md` artifact — so the technical direction is agreed and documented before planning or implementation begins.

### [`/apw-plan`](apw-plan.md) — Structured planning

Absorb the inputs, state your understanding, then produce planning docs at the depth the task warrants — FULL (requirements + design + plan), MEDIUM (design + plan), or PLAN — with every task tracing back to what it implements, so the plan survives the conversation and doesn't drift from intent.

### [`/apw-find-root-cause`](apw-find-root-cause.md) — Structured root-cause diagnosis

Capture the symptom, reproduce it, hold competing hypotheses, then converge on the cause through an iterative evidence loop and prove the causal chain — so the fix targets the underlying defect, not the first plausible story or the symptom one level up.

### [`/apw-implement`](apw-implement.md) — Structured implementation

Familiarize, ask, plan, confirm, implement, summarize — so you stop coding the wrong thing.

### [`/apw-write-test`](apw-write-test.md) — Structured test-writing

Establish the contract, derive expected behavior from the spec *before* reading the implementation, confirm the case list, write, then prove every test can fail before trusting its green — so the suite catches a wrong implementation instead of mirroring it. Works the same in TDD and post-hoc modes.

### [`/apw-verify`](apw-verify.md) — Structured verification

Establish a checkable target from the requirements, verify each item against reality with evidence, and record an honest verdict — so "done" is proven, not assumed.

### [`/apw-review`](apw-review.md) — Adversarial self-review

Switch from author to critic, attack your own diff, plan, or conclusion under explicit lenses, and record an honest verdict — so the flaw you're too close to see gets caught before it ships.

### [`/apw-commit`](apw-commit.md) — Guided commits

Draft from the diff against a project schema, review before staging, and keep history readable.

### [`/apw-onboard`](apw-onboard.md) — Codebase orientation

Survey the codebase once into a `Codebase Orientation` section of `AGENTS.md` — architecture, entry points, build/test/run commands, conventions — and orient from it in later sessions by verifying it against reality before trusting it, so sessions stop re-deriving the codebase from scratch and a stale map gets caught instead of believed. User-invoked only.

### [`/apw-handoff`](apw-handoff.md) — Session continuity

Checkpoint a task's in-flight state — done/next, decisions with their why, dead ends — into the work item's `handoff.md`, then resume in a fresh session by verifying that snapshot against reality before continuing — so a context reset costs minutes, not the re-derivation of a whole session.

### [`/apw-learn`](apw-learn.md) — Durable project memory

Distill a lesson — from the conversation, from user input, or from something you figured out — into `AGENTS.md`, but only after the user confirms, so the project stops re-learning the same things every session.

### [`apw-work-with-work-item-artifacts`](apw-work-with-work-item-artifacts.md) — Where artifacts live

A reference skill, not a workflow: the single source of truth for where a work item's artifacts live (`docs/work-items/<slug>/`), so the convention is defined once and every other skill points at it instead of restating the path.

## Commands

Commands compose the skills above into a multi-agent workflow. Unlike the skills, they are Claude-Code-specific (a slash command plus subagents).

### [`/apw-implement-verify-review`](apw-implement-verify-review.md) — Full implement → verify → review workflow

Carry a straightforward task end to end by orchestrating one subagent per phase — implement, verify, review — each in its own fresh context. The verify⇄implement loop closes automatically until verification passes, the review is run by an agent that didn't write the code, and every review finding comes back to you to accept, skip, or send for root-cause diagnosis first — so independence between building, proving, and critiquing is preserved instead of collapsing into one self-approving context.

### [`/apw-implement-review`](apw-implement-review.md) — Implement → review workflow (no verify)

The verification-free sibling of `/apw-implement-verify-review`, for tasks with nothing concrete to verify against — a trivial change, or one with no spec/plan/reproduction to check against. It orchestrates implement → review in separate contexts so the reviewer never wrote the code, and brings every finding back to you to accept, skip, or diagnose first — dropping the verify phase honestly instead of faking a hollow one.

---

Part of [AgenticPW](../../README.md) — a practical collection of agentic practices and workflows.
