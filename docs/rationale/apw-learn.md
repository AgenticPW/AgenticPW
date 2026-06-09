[← All rationales](index.md)

# apw-learn

`/apw-learn` (in a chat with lessons to mine) · `/apw-learn "<rule to record>"` (in a fresh chat)

A workflow for turning a lesson into a durable project rule: determine the source → gather candidates → check the target → propose → confirm → apply. It trades a moment of review for a project that stops re-learning the same things every session.

## The bad practice it prevents

A coding agent has no memory across sessions. Everything it discovers in one chat — a convention it had to infer, a mistake the user corrected, a stated preference, a sharp-edged gotcha — vanishes when the context window resets. The next session starts blind and re-learns it the hard way.

**Without the skill:**

- **Lessons evaporate.** A correction the user made last week may as well never have happened; the agent repeats the mistake.
- **Re-learning tax.** Each session re-derives the same conventions from scratch, burning context and the user's patience.
- **Or the opposite — silent, sloppy memory.** An agent that *does* write to the project's rule file tends to dump every passing observation into it unprompted, bloating the always-on instructions with obvious advice nobody asked for.

## How the skill helps

It replaces both failure modes — forgetting everything, and recording everything — with a gated, deliberate distillation into `AGENTS.md`, the always-on file every future session reads.

1. **Determine the mode.** Lessons come from one of three places: an existing conversation to mine, user-supplied input in a fresh chat, or something the agent itself just figured out.
2. **Gather candidates.** Keep only what is durable, project-scoped, actionable, and not already recorded — discard the ephemeral and the obvious.
3. **Check the target.** Read `AGENTS.md` first to avoid duplicates and match its structure (or note that it must be created).
4. **Propose, don't write.** Show the exact text and section, with a one-line reason each rule earns its place. For self-initiated lessons, *ask* whether it's even worth recording.
5. **Confirm.** Nothing is written without explicit approval; partial approvals are honoured.
6. **Apply.** Land the confirmed rules concisely in the right section, creating the file if needed.

> **The core idea:** memory is only useful if it's *trustworthy*. The skill makes persistence deliberate — the user decides what becomes a permanent rule — so `AGENTS.md` stays a curated set of real lessons, not a junk drawer. That confirmation gate is also what makes it safe for the agent to invoke on its own.

## Sample walkthrough

The most valuable mode is the one the user never explicitly triggers: the agent noticing, mid-task, that it just learned something worth keeping.

### 1 · A correction lands during normal work

```
user: no — we always run migrations through `make db-migrate`,
      never raw alembic. it wraps the env setup.
assistant: Got it, using `make db-migrate`. … (finishes the task)
```

### 2 · The agent self-invokes apw-learn and proposes — doesn't write

```
assistant: That migration convention isn't in AGENTS.md, and the next
session would hit the same wall. Worth recording?

  Section: ## Conventions
  + Run DB migrations via `make db-migrate`, never raw alembic — it
    wraps required env setup.

  Add it?
user: yes
```

### 3 · Apply, then a one-line report

```
assistant: Added to AGENTS.md → Conventions.
```

The lesson is now in the file every future session reads — and it got there only because the user said yes, not because the agent decided unilaterally to remember.

---

Part of [AgenticPW](../../README.md) · The counterpart to context loss — see also the [apw-research rationale](apw-research.md) for keeping *findings* durable.
