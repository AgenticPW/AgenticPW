[← All rationales](index.md)

# apw-handoff

`/apw-handoff` (checkpoint, when a session is ending mid-task) · `/apw-handoff "<slug>"` (resume, in a fresh session)

A session-continuity workflow with two modes: **checkpoint** — capture the in-flight state of a task (done / in flight / next, decisions with their why, dead ends, open questions, pointers) into the work item's `handoff.md` — and **resume** — read that handoff in a fresh session, verify it against reality, play the state back for confirmation, and continue. It trades two minutes of writing at the end of a session for not re-deriving an afternoon of work at the start of the next one.

## The bad practice it prevents

A task that outlives one context window gets cut mid-stride: the session ends, compaction runs, or the chat is simply closed. The durable *outputs* survive — the plan, the diff, the artifacts — but the working state between them doesn't. The next session starts from the artifacts and the diff alone and has to reconstruct everything else.

**Without the skill:**

- **Re-derivation tax.** The fresh session burns its opening context re-reading the codebase and guessing where the work stood, instead of continuing it.
- **Re-litigated decisions.** Choices that were settled — with good reasons — get re-opened and sometimes re-decided differently, because the reasons left with the old context.
- **Dead ends walked twice.** The most expensive knowledge from the last session is what *didn't* work; nothing records it, so the same plausible-looking approaches get tried again.
- **The lost next step.** The previous session knew exactly what to do next; the new one guesses, and half-finished edits get misread as finished work.
- **Stale resumption.** When notes *do* exist, they're trusted blindly — even though the repo may have moved since they were written.

## How the skill helps

It splits the problem at the context boundary: one mode writes the state down before it is lost, the other re-enters through it instead of from zero.

**Checkpoint (on the way out):**

1. **Gather the in-flight state.** Objective, state of play (done / in flight / next), each decision *with its why*, dead ends and why they failed, open questions, and exact pointers — files and line ranges, commands, sibling artifacts.
2. **Write `handoff.md`.** Into the work item folder, from the template — as a **living document** that is updated on every checkpoint, never an append-only log; history lives in git.
3. **Report.** The artifact path and the recorded next action — the artifact is the deliverable.

**Resume (on the way in):**

1. **Read the handoff and its context.** The `handoff.md`, then the artifacts and files it points to.
2. **Verify against reality.** The handoff records what *was* true; check the working tree, the named files, and whether "next" already happened. Where reality disagrees, reality wins.
3. **State your understanding and confirm.** Play the state back to the user — including discrepancies — before touching anything; priorities may have changed since the checkpoint.
4. **Continue the work.** Through the same skill the previous session was using, honouring recorded decisions and dead ends — and checkpoint again if this session ends with the task still open.

> **The core idea:** a context window is working memory, not storage — anything still in flight when it resets is gone. Checkpoint the state the artifacts don't carry (the next step, the why behind each decision, the paths already ruled out), and make the resuming session verify that snapshot against reality instead of trusting it. Two minutes of writing on the way out buys back the hours the next session would spend rediscovering — or re-breaking — what this one already knew.

## Sample walkthrough

A refactor outgrows the session. Watch the state survive the reset — and the resume catch what changed in between.

### 1 · Checkpointing on the way out

```
user: I have to stop here — /apw-handoff
assistant: Checkpoint mode. Wrote docs/work-items/extract-billing-service/handoff.md:

  Done: BillingService extracted, 14/16 call sites migrated (verified by tests).
  In flight: invoice.ts:88-130 half-migrated — old and new paths both live.
  Next: migrate the 2 call sites in legacy/cron.ts, then delete the old path.
  Decisions: kept the public API of charge() unchanged (callers in 3 repos).
  Dead ends: tried strangler-fig via a proxy class — broke DI resolution, reverted.

  Next action on record: migrate legacy/cron.ts call sites.
```

### 2 · Resuming in a fresh session

```
user: /apw-handoff "extract-billing-service"
assistant: Resume mode. Read handoff.md and the plan it links. Verifying against
  the repo before trusting it:

  - invoice.ts matches the recorded half-migrated state ✓
  - legacy/cron.ts — one of the two call sites is already migrated; commit
    b3e91a2 (yours, yesterday) did it after the checkpoint.

  So the state is: 15/16 migrated, one call site left in legacy/cron.ts:41,
  then delete the old path in invoice.ts. The proxy-class approach is on
  record as a dead end — I won't retry it. Continue from there?
user: yes
assistant: Migrating the last call site...
```

The handoff said two call sites remained; reality said one. Verification caught it, so the session continued from the true state instead of re-doing a migrated call site — and the dead end stayed dead instead of being rediscovered the hard way.

---

Part of [AgenticPW](../../README.md) · Complements the [apw-learn rationale](apw-learn.md) (learn persists durable *lessons* across sessions — this persists a task's in-flight *state*); the artifact lives with the work item per [apw-work-with-work-item-artifacts](apw-work-with-work-item-artifacts.md).
