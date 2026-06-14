---
description: Structured task-decomposition workflow. Use when a task needs to be mapped to an ordered sequence of skills before any work begins — to decide the right workflow rather than jumping straight to implementation or reflexively running the full research→design→plan→implement→verify→review stack.
disable-model-invocation: false
metadata:
  version: "1.0"
---

Use this skill when a task has been handed in but the right *sequence of skills* to apply is not yet decided. It sits before every other skill in the workflow: it maps the task to the minimal set of steps that will actually be needed, so later phases do not waste effort or skip work that matters. Follow the steps below strictly and in order.

## Step 1 — Absorb the task

Before assessing anything, read everything that defines the task:

- **The brief** — the task description, ticket, or request as given.
- **Existing artifacts** — if your work item already has a `research.md`, `brainstorm.md`, `tech-design.md`, `plan.md`, or similar artifact (see `apw-work-with-work-item-artifacts` for where they live), read them — they may already answer questions that would otherwise trigger additional steps.
- **The codebase** — enough of the affected area to know whether the change is local or cross-cutting, what patterns already exist, and what external dependencies are involved.

Do not skip this step. A workflow built without reading the affected code is a guess.

## Step 2 — Ask clarifying questions (only if needed)

If anything remains genuinely ambiguous after Step 1 — something that would change which steps are needed or in what order — ask up to 5 targeted questions.

Skip this step entirely if the task is clear. Do not ask for the sake of asking.

**Wait for the user's answers before proceeding.**

## Step 3 — Present the proposed workflow and confirm

Present your honest self-assessment and proposed workflow in chat:

**Self-assessment.** State what you know and what you don't, organized by dimension:

- **Requirements** — is the "what to build" clear, or fuzzy?
- **Technical unknowns** — are there APIs, libraries, or systems you're not sure about?
- **Design space** — are there meaningful architectural choices with real tradeoffs?
- **Task size** — is it large enough to need sub-task ordering before coding?
- **Type** — feature, bug-fix, refactor, or other? (bugs mandate root-cause first)

**Proposed workflow.** An ordered list of skills, each with a one-to-two sentence rationale explaining *why this specific step is needed for this specific task*. Use only the steps that are genuinely needed — do not add steps "just in case."

Available skills and when to include them:

| Skill | Include when |
|---|---|
| `apw-brainstorm` | The approach space is genuinely unclear and options need exploring before committing |
| `apw-research` | Specific technical questions must be answered before a design or implementation can be settled; scope it tightly to the actual unknowns |
| `apw-tech-design` | There are meaningful architectural choices with real tradeoffs that need evaluation |
| `apw-plan` | The work is large enough to need ordered, checkable sub-tasks before coding begins |
| `apw-implement` | The what-and-how is settled enough to build |
| `apw-write-test` | Tests need to be written before or alongside the implementation |
| `apw-verify` | There are concrete requirements or a spec to check the finished implementation against |
| `apw-review` | The finished work deserves adversarial critique before being considered done |
| `apw-find-root-cause` | The task is a bug or defect fix — mandatory first step, no exceptions |

Calibration guidance — typical shapes for common task types:

- **Simple, well-understood task:** `apw-implement`
- **Clear task, large enough to need ordering:** `apw-plan` → `apw-implement`
- **Task with a specific technical unknown:** `apw-research` (scoped) → `apw-implement`
- **Bug fix:** `apw-find-root-cause` → `apw-implement` [→ `apw-verify`]
- **Complex or higher-stakes task:** `apw-research` → `apw-tech-design` → `apw-plan` → `apw-implement` → `apw-verify` → `apw-review`

Then ask: **"Does this workflow look right? Should I write workflow.md?"**

**Wait for the user's answer before proceeding.**

## Step 4 — Write the artifact and summarize

Once the user confirms, write the result to your work item's **`workflow.md`** artifact — see `apw-work-with-work-item-artifacts` for where work-item artifacts live — using `.claude/skills/apw-decompose/WORKFLOW_TEMPLATE.md` as the structure.

Then output a concise summary:

- The number of steps and the overall shape of the workflow.
- Any notable conditions or risks that could change the workflow mid-execution.
- The path to the artifact.
