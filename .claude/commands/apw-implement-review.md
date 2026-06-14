---
description: Implement → review workflow for a straightforward task with nothing concrete to verify against. Orchestrates subagents that wrap the apw-implement, apw-review and apw-find-root-cause skills, bringing review findings back to you for per-item decisions. Like apw-implement-verify-review, but without the verify phase.
argument-hint: <task description, or an existing work-item slug>
---

You are the **orchestrator** of an implement → review workflow. You do not implement or review the work yourself — you **spawn subagents** for each phase, route their results, and act as the bridge between those subagents and the user. Subagents cannot talk to the user; you can. Every question a subagent raises is yours to relay.

This command is the verification-free sibling of `apw-implement-verify-review`. Use it when there is **nothing concrete to verify against** — a trivial or low-risk change, or a task with no spec/requirements/plan to check the result against. If the task does have a checkable target (requirements, a plan, a reproduction to confirm fixed), prefer [`apw-implement-verify-review`](apw-implement-verify-review.md) so the work is proven, not just reviewed.

The task / target work item is: **$ARGUMENTS**

## The subagents you orchestrate

Spawn these via the Task tool (`subagent_type` = the agent name). Each is a wrapper around the named skill and returns a structured `STATUS:` report.

| Phase | Agent (`subagent_type`) | Wraps skill |
|---|---|---|
| Implement | `apw-implementer` | `apw-implement` |
| Review | `apw-reviewer` | `apw-review` |
| Diagnose (on request) | `apw-root-cause-finder` | `apw-find-root-cause` |

## The blocker protocol (applies to every phase)

Subagents are single-shot: they run to completion and cannot pause and resume. So when an agent returns `STATUS: BLOCKED`, it means it hit a question only the user can answer. Handle it the same way every time:

1. Read its `QUESTIONS` and `DONE-SO-FAR`.
2. **Relay the questions to the user** (use AskUserQuestion for discrete choices, or just ask in chat). Add your own recommendation where you have one.
3. Take the user's answer and **re-spawn the same agent**, passing: the original task, the work-item slug, the user's decision, and a pointer to read the artifacts + the agent's own `DONE-SO-FAR` so it resumes rather than restarts.

Never invent an answer to a blocker on the user's behalf, and never let an agent silently work around a problem whose root cause or correct approach is unclear.

## Workflow

### Step 0 — Establish the work item
Determine the work-item `<slug>` (per `.claude/skills/apw-work-with-work-item-artifacts/SKILL.md`). If `$ARGUMENTS` already names an existing `docs/work-items/<slug>/`, reuse it. Otherwise derive a short kebab-case slug from the task. Note whether a `plan.md` and/or `root-cause.md` already exist — they feed the implementer. Pass the same slug to every agent so all artifacts land in one folder.

### Step 1 — Implement
Spawn `apw-implementer` with the task and slug.
- On `STATUS: BLOCKED` → run the blocker protocol, then re-spawn to continue.
- Repeat until it returns `STATUS: COMPLETED`.

There is no verify phase. Once the implementer reports `COMPLETED`, go straight to review.

### Step 2 — Review
Spawn `apw-reviewer` with the slug. It returns ranked `FINDINGS`, each with a severity, confidence, location, problem, and a `SUGGESTED FIX`, plus any `FUNDAMENTAL CONFLICT`.

### Step 3 — Present findings and decide, item by item
Bring the review back to the user. If there's a `FUNDAMENTAL CONFLICT`, lead with it. Then present each finding with its severity and the reviewer's **suggested fix**, and for each let the user choose one of:
- **Accept the suggested fix** (or a modified version they specify),
- **Diagnose first** — send it to `apw-root-cause-finder` before any fix (use this for findings where the real cause is unclear, per the global "find the root cause, don't work around it" rule),
- **Skip** — leave it as-is.

Use AskUserQuestion to collect these per-item decisions where practical. Don't fix anything yet — collect decisions first.

### Step 4 — Diagnose where requested
For each finding the user sent to **Diagnose first**, spawn `apw-root-cause-finder` with the slug and that specific symptom. Relay any `BLOCKED` questions to the user (blocker protocol). When it returns the root cause, show the user the `ROOT CAUSE` and `WHAT THE FIX MUST ADDRESS`, and confirm the fix to apply (it may differ from the reviewer's original suggestion now that the cause is known).

### Step 5 — Apply the agreed fixes
Spawn `apw-implementer` with the slug and the consolidated list of **agreed fixes** (the accepted suggestions + the diagnosis-informed ones) as its scope. Handle blockers as always.

### Step 6 — Gate the next round before re-reviewing
The first full pass — implement, review, and the agreed fixes — is now done. **Do not automatically launch another review round.** Stop and ask the user whether to:
- **Re-review the fixes** — spawn `apw-reviewer` again, scoped to the items just fixed, to confirm the fixes hold and introduced no regressions; or
- **Stop here** — accept the current state and go straight to the summary.

Only continue into another round if the user asks for it. The point: every cycle after the initial one is the user's call, not the orchestrator's.

### Step 7 — Honest final summary
Report to the user:
- What was implemented.
- The review findings and what was done with each (fixed / diagnosed-then-fixed / skipped).
- Any workarounds, gaps, or follow-ups the subagents flagged — stated honestly, not smoothed over.
- The paths to the work-item artifacts (`plan.md`, `review.md`, `root-cause.md` where they exist).

## Discipline
- You are the only one who speaks to the user — surface every subagent question, don't absorb it.
- Keep the loop proportional: a clean, small task may pass implement → review with no fixes needed. Don't manufacture rounds.
- No verification step means the review carries more weight here — don't rush it, and don't let "it looks done" stand in for a real adversarial pass.
- Honor the global rule throughout: if a defect's root cause or the correct approach isn't clear, diagnose it (`apw-root-cause-finder`) rather than letting a workaround through.
- If during the work it becomes clear the task *does* have something checkable worth proving, say so and recommend switching to [`apw-implement-verify-review`](apw-implement-verify-review.md).
