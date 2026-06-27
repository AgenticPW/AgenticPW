---
description: Checkpoint in-flight task state to the work item, and resume from that checkpoint in a fresh session. Use when a session is ending mid-task, when context is about to be compacted or reset, or when picking up a task a previous session left unfinished.
disable-model-invocation: false
metadata:
  version: "1.0"
---

Use this skill to carry **in-flight task state** across a context reset. It exists to fight one specific limitation: a model's **context is finite, and compaction or a new session erases mid-task state** — what was done, what was about to happen, which decisions were made and why, and which approaches already failed. Durable *outputs* survive in the work item's artifacts and `apw-learn` persists durable *lessons*, but the working state between them evaporates — so a fresh session re-derives the plan, re-makes settled decisions, and re-walks dead ends. The fix is a checkpoint artifact: write the state down before it is lost, and re-enter through it instead of from zero.

The skill has two modes — **checkpoint** (save state on the way out) and **resume** (restore state on the way in). Determine the mode first, then follow that mode's steps strictly and in order.

## Output style

When this skill asks for a plan, report, summary, status update, or other chat-facing output, prefer bullet points or numbered lists over plain paragraphs. Keep each item concise and content-bearing. Omit language that only smooths tone, repeats context, or adds "language sugar" without changing the meaning.

## Step 1 — Determine the mode

- **Checkpoint** — a session is ending mid-task: the user is stopping for now, context is close to compaction, or you are partway through work that another session will finish. The deliverable is an up-to-date `handoff.md`.
- **Resume** — you are picking up a task with little or no conversation history, and the work item has a `handoff.md` from a previous session. The deliverable is verified, confirmed continuation of the work.

State which mode you are in before continuing. If asked to resume but no `handoff.md` exists for the work item, say so plainly and fall back to the artifacts that do exist — do not invent a prior state.

## Checkpoint mode

### Step C1 — Gather the in-flight state

Collect, from the conversation and the working tree, everything a fresh session would otherwise have to re-derive:

- **The objective** — what the task is trying to achieve, in one or two sentences, and the work item it belongs to.
- **State of play** — what is **done** (and verified or not), what is **in flight** (started but unfinished, including half-edited files), and what is **next** (the concrete next action, not a vague direction).
- **Decisions made** — each settled decision *with its why*. A decision recorded without its reason will be re-litigated.
- **Dead ends** — approaches already tried and abandoned, and why they failed. This is the most expensive knowledge to lose, because a fresh session will reach for the same plausible ideas.
- **Risks** — anything the next session should carry forward.
- **Open questions** — anything unresolved, especially questions waiting on the user.
- **Pointers** — the specific files and line ranges in play, relevant commands, and the sibling artifacts (`plan.md`, `root-cause.md`, …) that hold the task's durable context.

Prefer precise references (`path/to/file.ts:42`, commit SHAs, exact commands) over prose descriptions — the reader cannot ask you follow-up questions.

### Step C2 — Write the artifact

Write the state to your work item's **`handoff.md`** artifact — see `apw-work-with-work-item-artifacts` for where work-item artifacts live — using `.claude/skills/apw-handoff/HANDOFF_TEMPLATE.md` as the structure.

`handoff.md` is a **living document**: it describes the *current* state of the task, so update or overwrite it on every checkpoint rather than appending a new entry. History lives in version control; a resuming session must not have to dig through stale layers to find the live state.

### Step C3 — Report

Confirm in one or two lines: the path to the artifact, and the single next action it records. Do not restate the contents — the artifact is the deliverable.

## Resume mode

### Step R1 — Read the handoff and its context

Read the work item's `handoff.md`, then the sibling artifacts and files it points to — the plan, the diagnosis, the in-flight files. Read enough to *understand* the recorded state, not just to repeat it.

### Step R2 — Verify against reality

The handoff records what was true when it was written; the repository may have moved since. Before trusting it, check the recorded state against reality:

- Does the working tree match (git status, branch, uncommitted changes the handoff mentions)?
- Do the files, functions, and line references it names still exist as described?
- Has anything it lists as "next" already been done — by the user, another session, or a merge?

Where reality disagrees with the handoff, **reality wins**. Note each discrepancy explicitly; it changes what "next" means.

### Step R3 — State your understanding and confirm

Before touching anything, play the state back to the user: the objective, where the task stands, any discrepancies found in Step R2, and the next action you intend to take. Ask the user to confirm or correct it — priorities may have changed since the checkpoint, and the handoff cannot know that.

### Step R4 — Continue the work

Once confirmed, continue the task from the recorded state — through the same skill the previous session was using (`apw-implement`, `apw-plan`, …) where one applies. Honour the recorded decisions and dead ends: do not re-open a settled decision or retry a recorded dead end without new information, and if new information *does* justify it, say so explicitly rather than silently diverging.

When the session ends and the task is still unfinished, checkpoint again so the chain is never broken.
