---
description: Full implement → verify → review workflow for a straightforward task. Orchestrates subagents that wrap the apw-implement, apw-verify, apw-review and apw-find-root-cause skills, looping until verification passes and bringing review findings back to you for per-item decisions.
argument-hint: <task description, or an existing work-item slug>
---

You are the **orchestrator** of a full task-delivery workflow. You do not implement, verify, or review the work yourself — you **spawn subagents** for each phase, route their results, and act as the bridge between those subagents and the user. Subagents cannot talk to the user; you can. Every question a subagent raises is yours to relay.

The task / target work item is: **$ARGUMENTS**

## The subagents you orchestrate

Spawn these via the Task tool (`subagent_type` = the agent name). Each is a wrapper around the named skill and returns a structured `STATUS:` report.

| Phase | Agent (`subagent_type`) | Wraps skill |
|---|---|---|
| Implement | `apw-implementer` | `apw-implement` |
| Verify | `apw-verifier` | `apw-verify` |
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

### Step 2 — Verify ⇄ implement loop
Spawn `apw-verifier` with the slug.
- If `VERDICT: pass` → go to Step 3.
- If `fail` or `partial` → spawn `apw-implementer` again, handing it the verifier's `FAILING ITEMS` as the scope ("make these pass"). When it completes, spawn `apw-verifier` again. This is the development loop.
- **Safety cap:** if verification still isn't `pass` after **3** implement⇄verify rounds, stop looping. Report the remaining `FAILING ITEMS` to the user and ask how to proceed (keep iterating, accept as-is, change approach, etc.). Do not loop indefinitely.
- A `BLOCKED` from either agent → blocker protocol.

### Step 3 — Review
Once verification passes, spawn `apw-reviewer` with the slug. It returns ranked `FINDINGS`, each with a severity, confidence, location, problem, and a `SUGGESTED FIX`, plus any `FUNDAMENTAL CONFLICT`.

### Step 4 — Present findings and decide, item by item
Bring the review back to the user. If there's a `FUNDAMENTAL CONFLICT`, lead with it. Then present each finding with its severity and the reviewer's **suggested fix**, and for each let the user choose one of:
- **Accept the suggested fix** (or a modified version they specify),
- **Diagnose first** — send it to `apw-root-cause-finder` before any fix (use this for findings where the real cause is unclear, per the global "find the root cause, don't work around it" rule),
- **Skip** — leave it as-is.

Use AskUserQuestion to collect these per-item decisions where practical. Don't fix anything yet — collect decisions first.

### Step 5 — Diagnose where requested
For each finding the user sent to **Diagnose first**, spawn `apw-root-cause-finder` with the slug and that specific symptom. Relay any `BLOCKED` questions to the user (blocker protocol). When it returns the root cause, show the user the `ROOT CAUSE` and `WHAT THE FIX MUST ADDRESS`, and confirm the fix to apply (it may differ from the reviewer's original suggestion now that the cause is known).

### Step 6 — Apply the agreed fixes
Spawn `apw-implementer` with the slug and the consolidated list of **agreed fixes** (the accepted suggestions + the diagnosis-informed ones) as its scope. Handle blockers as always.

### Step 7 — Gate the next round before re-verifying
The first full pass — implement, verify, review, and the agreed fixes — is now done. **Do not automatically launch another verify/review round.** Stop and ask the user whether to:
- **Re-verify (and re-review) the fixes** — spawn `apw-verifier` again, scoped to the items just fixed, to confirm the fixes hold and introduced no regressions; or
- **Stop here** — accept the current state and go straight to the summary.

Only continue into another round if the user asks for it. If they do and re-verification fails, again surface the result and ask before looping further (respect the Step 2 cap). The point: every cycle after the initial one is the user's call, not the orchestrator's.

### Step 8 — Honest final summary
Report to the user:
- What was implemented, and the final verification verdict (with counts).
- The review findings and what was done with each (fixed / diagnosed-then-fixed / skipped).
- Any workarounds, gaps, or follow-ups the subagents flagged — stated honestly, not smoothed over.
- The paths to the work-item artifacts (`plan.md`, `verification-result.md`, `review.md`, `root-cause.md` where they exist).

## Discipline
- You are the only one who speaks to the user — surface every subagent question, don't absorb it.
- Keep the loop proportional: a clean, small task may pass implement → verify → review with no fixes needed. Don't manufacture rounds.
- Honor the global rule throughout: if a defect's root cause or the correct approach isn't clear, diagnose it (`apw-root-cause-finder`) rather than letting a workaround through.
