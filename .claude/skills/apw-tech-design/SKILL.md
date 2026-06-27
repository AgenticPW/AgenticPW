---
description: Structured technical design workflow. Use when a task needs a concrete technical solution design — options, tradeoffs, and a recommendation — before implementation begins.
disable-model-invocation: false
metadata:
  version: "1.0"
---

Use this skill when a task needs a *technical solution design* before anyone starts building it. It sits between open-ended ideation (`apw-brainstorm`) and full planning (`apw-plan`): it takes a task description, explores the codebase and constraints, then converges to a concrete technical recommendation the user can confirm before any detailed planning or code begins. Follow the steps below strictly and in order.

## Output style

When this skill asks for a plan, report, summary, status update, or other chat-facing output, prefer bullet points or numbered lists over plain paragraphs. Keep each item concise and content-bearing. Omit language that only smooths tone, repeats context, or adds "language sugar" without changing the meaning.

## Step 1 — Familiarize

Before forming any opinion, read everything relevant to the task:

- **The task description** — exactly what needs to be built or changed.
- **The codebase** — files, modules, and conventions the change will touch; existing patterns that constrain or enable each option.
- **Prior artifacts** — if your work item's `research.md` or `brainstorm.md` artifacts exist (see `apw-work-with-work-item-artifacts` for where they live), read them; the design builds on them, it does not redo their work.
- **External constraints** — API contracts, dependencies, performance requirements, or anything else that would make an option non-viable.

Do not skip this step, even if the task looks small. An option that ignores an existing pattern or a known constraint wastes the user's time.

## Step 2 — Ask clarifying questions (only if needed)

If anything remains genuinely ambiguous after Step 1 — something that would change which options are viable or how they are ranked — ask up to 5 targeted questions.

Skip this step entirely if the task is clear. Do not ask for the sake of asking.

**Wait for the user's answers before proceeding.**

## Step 3 — Present options and confirm

Present up to 3 high-level technical options. For each option:

- **Name** — a short label.
- **Approach** — one or two sentences on how it works.
- **Pros** — concrete advantages in this context.
- **Cons** — concrete drawbacks or risks.

End with a clear **recommendation**: which option you would choose and the single most important reason why. It is fine to present only one option if there is no meaningful alternative — do not manufacture choices to hit a number.

Then ask: **"Should I write the full tech-design doc?"**

**Wait for the user's answer before proceeding.**

## Step 4 — Write the artifact and summarize

Once the user confirms, write the result to your work item's **`tech-design.md`** artifact — see `apw-work-with-work-item-artifacts` for where work-item artifacts live — using `.claude/skills/apw-tech-design/TECH_DESIGN_TEMPLATE.md` as the structure.

Then output a concise summary covering:

- The **recommended option** and the core rationale.
- **Risks** — risks the implementer must carry.
- **Open questions** — missing context, unknowns that remain, or assumptions that must be validated before implementation.
- The **path to the artifact**.

Be honest. If something is missing or uncertain, say so here rather than burying it in the doc.
