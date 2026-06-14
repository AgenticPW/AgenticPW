[← All rationales](index.md)

# apw-decompose

`/apw-decompose "<task description>"`

A structured task-decomposition workflow: absorb → ask (if needed) → present proposed workflow → confirm → write artifact & summarize. It sits before every other skill in the workflow: it maps a task to the minimal ordered sequence of skills actually needed, so you never jump straight to implementation when the approach is unclear, and never run the full research→design→plan→implement→verify→review stack when a simple implement would do.

## The bad practice it prevents

When a task arrives, the instinct is to pick a workflow without examining the task — defaulting to either "just implement" or "do everything." Both are wrong without the task in front of you.

**Without the skill:**

- **No self-assessment.** The agent never asks what it knows and what it doesn't, so the workflow is chosen from habit rather than from the task.
- **Over-scoped.** A two-line change gets research, design, planning, verification, and review added to it — effort that produces no value and adds friction.
- **Under-scoped.** A complex, approach-sensitive task gets implemented immediately — the gaps surface halfway through and the work has to restart from a cleaner state.
- **Invisible reasoning.** The agent never makes its workflow decision explicit, so the user cannot correct it before work begins.
- **Wrong skills in the wrong order.** Verification is added where there's no spec to verify against, or root-cause diagnosis is skipped on a bug fix and a guess is implemented instead.

## How the skill helps

It inserts a lightweight decision step — a few minutes of thought and one round of confirmation — before any skill runs:

1. **Absorb the task.** Read the brief, any prior artifacts for this work item, and enough of the codebase to understand the scope. Do not skip this — a workflow built without reading the code is a guess.
2. **Ask if needed.** Resolve genuine ambiguity — up to 5 questions — that would change which steps are included or in what order. Skip if the task is already clear. Wait for answers.
3. **Present the proposed workflow.** State an honest self-assessment (what's clear, what's unknown, what makes this easy or complex), then propose the ordered list of skills with a one-to-two sentence rationale per step. Ask: **"Does this workflow look right? Should I write workflow.md?"** Wait for confirmation.
4. **Write & summarize.** Produce `workflow.md`, then report the shape of the workflow and any conditions that could change it mid-execution.

> **The core idea:** choosing the right set of skills is itself a decision that deserves a step. A two-minute decomposition upfront prevents a session of backtracking. The agent's self-assessment — what it knows, what it doesn't, what makes the task easy or risky — is what makes the workflow choice defensible instead of reflexive.

## Calibrated to the task

The workflow proposed is the *minimum* set of steps that the task genuinely needs. Common shapes:

| Task type | Typical workflow |
|---|---|
| Simple, well-understood task | `apw-implement` |
| Clear task, large enough to need ordering | `apw-plan` → `apw-implement` |
| Task with a specific technical unknown | `apw-research` (scoped) → `apw-implement` |
| Bug or defect fix | `apw-find-root-cause` → `apw-implement` [→ `apw-verify`] |
| Complex or higher-stakes task | `apw-research` → `apw-tech-design` → `apw-plan` → `apw-implement` → `apw-verify` → `apw-review` |

The skill does not add steps "just in case." `apw-review` is not added to every task — only when the finished work genuinely warrants adversarial critique before shipping. `apw-verify` is not added unless there's a concrete spec or set of requirements to verify against.

The artifact is `workflow.md` (location per [`apw-work-with-work-item-artifacts`](apw-work-with-work-item-artifacts.md)), structured by [`WORKFLOW_TEMPLATE.md`](../../.claude/skills/apw-decompose/WORKFLOW_TEMPLATE.md): Task → Assessment → Workflow steps → Conditions and risks.

## Sample walkthrough

A developer has just been handed a task: fix a slow query that's timing out in production.

### 1 · The request

```
user: /apw-decompose "the /reports endpoint is timing out in prod — needs fixing"
```

### 2 · Absorb, then propose

```
assistant: Read the endpoint. It runs a JOIN across three tables with no index on
the date filter column; the query plan confirms a full table scan at ~2M rows.

Self-assessment:
  Requirements: clear — the endpoint must not time out.
  Technical unknowns: none significant — the slow path is visible in the query plan.
  Design space: no meaningful choices; the fix is an index + query rewrite.
  Task size: small, fits in one session.
  Type: bug-fix — a known, observable defect.

Proposed workflow:

  Step 1: apw-find-root-cause — confirm the exact cause and prove the causal chain
  before any fix, per the global "find the root cause, don't work around it" rule.
  Produces: root-cause.md

  Step 2: apw-implement — apply the proven fix (index + query rewrite).
  Produces: implementation in codebase

  Step 3: apw-verify — check the fix resolves the timeout against the reproduction
  case established in root-cause.md.
  Produces: verification-result.md

Does this workflow look right? Should I write workflow.md?
user: yes
```

### 3 · Write the artifact

```
assistant: Wrote docs/work-items/reports-timeout/workflow.md.

3 steps: find-root-cause → implement → verify.
Condition: if root cause turns out to be something other than the missing index
(e.g. an N+1 in the application layer), the implement step will change scope —
surface that before fixing.
```

A vague "fix the slow query" task became a concrete, confirmed three-step plan with an honest condition — before a single line of code changed.

---

Part of [AgenticPW](../../README.md) · See also the [apw-plan rationale](apw-plan.md) and [apw-implement rationale](apw-implement.md).
