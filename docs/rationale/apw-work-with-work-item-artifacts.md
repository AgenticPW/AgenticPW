[← All rationales](index.md)

# apw-work-with-work-item-artifacts

A reference skill — not a workflow. It is the single source of truth for **where a work item's artifacts live**, so the convention is defined in one place instead of restated everywhere.

## The bad practice it prevents

Every artifact-producing skill needs to say where its output goes — `research.md`, `plan.md`, `root-cause.md`, and the rest. The tempting shortcut is for each skill, template, and doc to spell out the full path itself. That works until the convention has to change.

**Without the skill:**

- **Scattered convention.** The same path rule is copied into 8 SKILL.md files, 8 templates, the README, and several rationale pages — there is no canonical statement of it.
- **Drift.** Because it's restated everywhere, the copies disagree over time: one says `docs/<slug>/`, another `docs/work-items/<slug>/`, and nobody can tell which is authoritative.
- **Laborious to change.** Moving the base path means hunting down and editing a dozen files, and missing one leaves a stale instruction behind.

## How the skill helps

It fixes the location in exactly one place and turns every other mention into a pointer.

- **One source of truth.** This skill defines the location: a work item's artifacts live together under `docs/work-items/<slug>/` in the project root, keyed by a short kebab-case slug.
- **A reused slug.** The same `<slug>` is reused across every skill that touches the same work item — research, plan, implementation, verification — so all of a work item's artifacts sit side by side in one folder.
- **A skill→filename table.** It maps each producing skill to its standard filename, so the full set of an item's sibling artifacts is documented in one place.
- **Pointers, not copies.** Every other skill keeps naming its own filename but says *"see `apw-work-with-work-item-artifacts` for where artifacts live"* — so the path lives in one file and nothing has to be kept in sync.

> **The core idea:** a convention restated in a dozen places isn't a convention, it's a dozen things that will eventually disagree. Define *where artifacts live* once, point everything else at it, and changing the location becomes a one-file edit instead of a repo-wide sweep.

## How to use it

You don't invoke it in a normal flow. The artifact-producing skills reference it by name; read it (or pull it up with the Skill tool) when you need to know where a work item's artifacts belong, or when you want to change the convention in one place. It carries no template and no steps — just the canonical location and the filename table.

---

Part of [AgenticPW](../../README.md) · Referenced by every artifact-producing skill — see the [apw-research](apw-research.md), [apw-plan](apw-plan.md), and [apw-implement](apw-implement.md) rationales.
