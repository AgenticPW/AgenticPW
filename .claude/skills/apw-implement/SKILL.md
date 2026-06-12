---
description: Structured implementation workflow. Use when asked to implement a feature, fix, or any change.
disable-model-invocation: false
metadata:
  version: "1.0"
---

Use this skill when asked to implement a feature, fix, or any change. Follow the steps below strictly and in order.

## Step 1 — Familiarize

Before doing anything else, read everything relevant to the task:
- Existing codebase files related to the area of change
- API docs, external documentation, or specs if the task touches integrations
- Any prior work (recent commits, related code) that could affect the approach
- Any planning artifact for this task — if your work item's `plan.md` artifact exists (from `apw-plan`; see `apw-work-with-work-item-artifacts` for where it lives), it is your task list and source of intent; read it, along with any `requirements.md` / `design.md` it references
- If the task is a fix, any diagnosis artifact — if your work item's `root-cause.md` artifact exists (from `apw-find-root-cause`; see `apw-work-with-work-item-artifacts` for where it lives), it names the proven defect the fix must address; read it before deciding the approach

Do not skip this step, even if the task looks simple. The goal is to have a complete picture before forming any opinion.

## Step 2 — Ask questions (up to 5, only if needed)

If anything is unclear after familiarizing yourself, ask up to 5 targeted questions. Questions should resolve genuine ambiguity that would change the implementation approach.

Skip this step entirely if the task is clear after Step 1. Do not ask questions for the sake of asking.

## Step 3 — Present a mini plan and confirm

Write a concise plan that states:
- What you are going to do (the approach)
- What files or systems will be changed
- Any notable decisions or tradeoffs

Then ask: **"Ready to proceed?"**

If the task is too large or complex to do well in one session, say so explicitly. Name the parts that should be handled first (research, API exploration, spec writing, etc.) and recommend tackling those before implementation begins. Do not start a partial implementation that will leave things in a broken state.

Wait for the user to confirm or correct the plan before moving to Step 4.

## Step 4 — Implement

Proceed with the implementation as agreed. Use the TodoWrite tool (if available) to track each step and mark tasks complete as you finish them — do not batch completions.

If you are working from an `apw-plan` `plan.md`, tick each `- [ ]` box to `- [x]` as you complete that task, so the plan stays a live record of what's done and what's left.

## Step 5 — Surface conflicts during implementation

If you discover something during implementation that contradicts the plan — an unexpected API shape, a constraint in the codebase, a missing dependency, a design conflict — stop and describe the issue to the user. Do not silently work around it or make a unilateral decision that changes the plan. Resume only after the user decides how to handle it.

## Step 6 — Honest summary

When done, provide a concise summary:
- What was implemented
- Any workarounds that had to be made and why
- Anything that could not be done properly (with the reason)
- Any known gaps or follow-up work that should happen next

Be honest. Do not present a clean picture if tradeoffs were made.
