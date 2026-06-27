---
description: Distill durable lessons from a conversation, user input, or your own work into the project's AGENTS.md — but only after the user confirms. Use after finishing a task, when the user wants to record a rule, or when you've figured out something worth persisting.
disable-model-invocation: false
metadata:
  version: "1.0"
---

Use this skill to turn a lesson into a durable project rule. It exists to fight one specific limitation: a model has **no memory across sessions** — conventions discovered, mistakes corrected, and preferences stated all evaporate when the context window resets, so the same ground gets re-learned and the same mistakes repeat. The fix is to write the lesson into the project's always-on instructions (`AGENTS.md`), which every future session reads. This skill is a **cross-cutting reflex**, not a chain stage: run it after any task, when the user hands you a rule, or when you yourself notice something worth keeping. The one non-negotiable rule: **never edit `AGENTS.md` without explicit user confirmation.** Follow the steps below in order.

## Output style

When this skill asks for a plan, report, summary, status update, or other chat-facing output, prefer bullet points or numbered lists over plain paragraphs. Keep each item concise and content-bearing. Omit language that only smooths tone, repeats context, or adds "language sugar" without changing the meaning.

## Step 1 — Determine the mode

Figure out which of three entry paths you are in, because the source of the candidate lessons differs:

- **A · Existing conversation.** The skill was invoked inside a chat that has real history. Mine that conversation for lessons.
- **B · New chat.** The skill was invoked with little or no prior context, and the user supplied the rule or data as arguments. The user's input *is* the candidate.
- **C · Agent-initiated.** You reached for this skill on your own, mid- or post-task, because you figured out something that future sessions should know. The thing you just learned is the candidate.

State which mode you're in before continuing.

## Step 2 — Gather candidate lessons

Collect the candidate rules according to the mode:

- **Mode A:** scan the conversation for lessons worth persisting — a convention discovered, a mistake the user corrected, a stated preference, a constraint, a non-obvious gotcha, a tool or command that worked.
- **Mode B:** take the user-provided data as the candidate. If it's vague or underspecified, ask a brief clarifying question before turning it into a rule.
- **Mode C:** state plainly the specific thing you figured out and why it surprised you.

A candidate qualifies as a rule only if it is:

- **Durable** — true beyond this one task, not an ephemeral detail of the current change.
- **Project-scoped** — about *this* project's conventions, constraints, or preferences; not generic best practice the agent already knows.
- **Actionable** — phrased so a future session can actually follow it.
- **Not already covered** — absent from `AGENTS.md` (checked in Step 3).

Discard candidates that fail these. Be ruthless: a thin `AGENTS.md` of real rules beats a bloated one full of obvious advice.

## Step 3 — Check the target file

Look for `AGENTS.md` in the repository root.

- **If it exists:** read it. Use it to (a) drop any candidate that's already covered, and (b) learn its existing section structure so additions land in the right place and match its style.
- **If it does not exist:** note that you'll create it with a sensible heading structure.

## Step 4 — Propose, don't write

Present the proposed additions to the user as concrete, reviewable text — the **exact wording** you'd add and the **section** it would go in. For each, give a one-line reason it's worth persisting (what future mistake it prevents or what it saves re-learning).

This is the central gate of the skill. Do not edit `AGENTS.md` in this step.

**For Mode C especially:** you arrived here on your own, so do not assume the user wants this recorded. Explicitly ask whether it makes sense to add it before adding — the user may consider it obvious, wrong, or too narrow to be worth a permanent rule.

## Step 5 — Confirm

Ask the user to confirm. They may approve everything, approve some, reword, or reject outright. Honour partial approvals exactly. **Never write to `AGENTS.md` without explicit confirmation** — this is the line that makes the skill safe to let the agent self-invoke.

## Step 6 — Apply

Once confirmed, edit (or create) `AGENTS.md`:

- Place each rule in the appropriate section; create the section if needed.
- Keep entries concise and imperative — a rule, not a paragraph.
- Don't duplicate or contradict existing rules; if a new rule supersedes an old one, update the old one rather than appending.
- If creating the file, give it a clear title and a small set of logical headings (e.g. conventions, constraints, preferences) so it stays organized as it grows.

## Step 7 — Report

Keep this to one or two lines. State only what was added and to which section (and anything skipped, with a short reason). Do not restate the rule text, re-explain why memory matters, or recap the steps — the user just watched it happen.
