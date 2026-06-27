---
description: Defines where a work item's artifacts live. Use as the single source of truth for the location of workflow, research, brainstorm, requirements, design, plan, root-cause, verification, review, handoff and other "apw-skills" documents.
disable-model-invocation: false
metadata:
  version: "1.0"
---

This skill is the single source of truth for **where a work item's artifacts live**. Other skills name the artifact they produce (its filename) and point here for the location; they do not restate the path.

## Output style

When this skill asks for a plan, report, summary, status update, or other chat-facing output, prefer bullet points or numbered lists over plain paragraphs. Keep each item concise and content-bearing. Omit language that only smooths tone, repeats context, or adds "language sugar" without changing the meaning.

## Where artifacts live

A work item's artifacts live together under **`docs/work-items/<slug>/`** in the project root.

`<slug>` is a short kebab-case name for the work item (a work item ≈ a task). **Reuse the same `<slug>` across every skill that touches the same work item** — research, plan, implementation, verification, and so on — so all of that work item's artifacts sit side by side in one folder.

## Example

For example, the research and plan for a work item slugged `payment-retry` live at `docs/work-items/payment-retry/research.md` and `docs/work-items/payment-retry/plan.md`.
