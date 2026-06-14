[← All rationales](index.md)

# apw-implement-review

`/apw-implement-review "<task description, or an existing work-item slug>"`

A **command**, not a skill. It runs a straightforward task end to end by orchestrating one subagent per phase — implement, then review — bringing the review findings back to you for a decision on each one. It is the verification-free sibling of [`apw-implement-verify-review`](apw-implement-verify-review.md): same independence between building and critiquing, but without a verify phase, for tasks where there is nothing concrete to verify against.

## Why a version without verify

[`apw-implement-verify-review`](apw-implement-verify-review.md) closes a verify⇄implement loop until verification passes. That loop only earns its cost when there is a **checkable target** — requirements, a plan, a spec, a reproduction to confirm fixed. Plenty of real work has none of that:

- A trivial or low-risk change where "verify each requirement against reality" would be ceremony, not signal.
- A task with no written spec or plan to verify against — the intent lives in the request and the diff, not in a checklist.
- Exploratory or cleanup work where what matters is "is this change sound?", a question a review answers and a verification pass cannot.

Forcing a verify phase onto those tasks produces a hollow verification — an agent inventing a checklist just to tick it — which is worse than no verification, because it *looks* like proof. This command drops the phase honestly rather than faking it.

## The bad practice it prevents

Even without verification, the review step is the one most easily lost. Running implement → review yourself, in a single conversation, quietly defeats the point of reviewing at all.

**Without the command:**

- **The reviewer is the author.** The same context that wrote the code reviews it — and a model is biased toward its own output, so the review rationalizes the work instead of attacking it. The flaw it just authored gets a "looks good to me."
- **Review collapses into "looks done."** Once the code appears finished, it's tempting to skip straight to shipping. With nothing forcing an adversarial pass, the review never really happens.
- **Findings vanish into a wall of text.** A review surfaces several issues; without a structured decision point, half are silently dropped and the rest are fixed without you ever choosing what mattered.
- **Symptoms get patched.** A review finding gets a quick workaround instead of a diagnosis, because stopping to find the real cause feels like a detour.

## How the command helps

It assigns each phase to a **dedicated subagent that wraps the matching skill** and runs in its own context, and it makes the orchestrator — the only party that talks to you — responsible for routing results and decisions.

| Phase | Subagent | Wraps |
|---|---|---|
| Implement | `apw-implementer` | [`apw-implement`](apw-implement.md) |
| Review | `apw-reviewer` | [`apw-review`](apw-review.md) |
| Diagnose (on request) | `apw-root-cause-finder` | [`apw-find-root-cause`](apw-find-root-cause.md) |

1. **Separate contexts beat author bias.** The reviewer never wrote the code; it sees the diff cold, the way a second engineer would. With no verify phase to lean on, this independence is the command's whole value — so it is preserved, not collapsed.
2. **You decide every review finding.** The reviewer returns ranked findings, each with a suggested fix. You choose per item: accept the fix, send it to the root-cause-finder to be **diagnosed first**, or skip it. Nothing is fixed before you've chosen.
3. **Root cause on demand.** When a finding's real cause is unclear, one choice routes it through a proper diagnosis before any code changes — so the fix targets the defect, not the symptom.
4. **Questions always reach you.** Subagents can't talk to you directly, so any genuine blocker a subagent hits is surfaced by the orchestrator and relayed back as a decision — never guessed, never silently worked around.
5. **Durable artifacts tie it together.** Every phase reads and writes the work item's artifacts (`plan.md`, `review.md`, `root-cause.md`) under `docs/work-items/<slug>/`, so the handoff between agents survives context loss and the trail is inspectable afterward.

> **The core idea:** the value of implement / review comes from them being *independent*. Collapse them into one context and the independence is lost — the author reviews itself and findings evaporate. This command keeps them separate and makes you the judge at the points that need judgment. When the task also has something worth *proving*, reach for [`apw-implement-verify-review`](apw-implement-verify-review.md) instead.

## When to use it — and when not

Use it for a **straightforward task with nothing concrete to verify against** — a trivial change, or one with no spec/plan/reproduction to check the result against — that you still want carried to a reviewed result without driving each phase by hand. If the task *does* have a checkable target, use [`apw-implement-verify-review`](apw-implement-verify-review.md) so the result is verified, not just reviewed. For a genuinely complex or fuzzy task, do the upstream thinking first — [`apw-research`](apw-research.md), [`apw-brainstorm`](apw-brainstorm.md), [`apw-plan`](apw-plan.md) — and let the resulting `plan.md` feed a command. For a bug whose cause isn't yet proven, diagnose with [`apw-find-root-cause`](apw-find-root-cause.md) first; the command will pick up the `root-cause.md`.

## Sample walkthrough

```
user: /apw-implement-review "rename the `tmp` helper to `scratch` across the cli package"
```

```
assistant (orchestrator):
  Work item: rename-tmp-helper

  → spawning apw-implementer …
  ← COMPLETED. Renamed `tmp` → `scratch` and updated 9 call sites.
    Files: cli/scratch.ts (renamed), 7 importers.

  → spawning apw-reviewer …
  ← VERDICT: revise. Findings:
    1. [should-fix] a dynamic `require("./tmp")` in cli/plugins.ts was
       missed — it still points at the old path and will throw at runtime.
    2. [nit] the rename left a stale `tmp` mention in a doc comment.
```

```
assistant: The review raised two items — how should I handle each?

  1. Missed dynamic require in plugins.ts → suggested fix: update the path
  2. Stale `tmp` in a doc comment → suggested fix: one-word edit
```

```
user: fix both as suggested.
  → spawning apw-implementer (fixes: #1 plugins.ts path, #2 doc comment) …
  ← COMPLETED.

assistant: Stop here, or re-review the fixes?
user: stop here.

  Summary: renamed `tmp` → `scratch` across the cli package (10 sites).
  Review: #1 (missed dynamic require) and #2 (stale doc comment) both
  fixed as suggested. Artifacts in docs/work-items/rename-tmp-helper/.
```

The review caught a runtime break the author's context waved through, and *you* decided what to do with each finding — all without a verify phase the task had nothing to feed.

---

Part of [AgenticPW](../../README.md). The command is Claude-Code-specific (a slash command plus subagents); the skills it orchestrates work in any tool that follows the Agent Skills standard.
