# Review: existing apw-* skill suite

- **Date:** 2026-06-10
- **Slug:** existing-skills-review
- **Target:** Seven SKILL.md files in `.claude/skills/` — `apw-brainstorm`, `apw-research`, `apw-implement`, `apw-verify`, `apw-review`, `apw-commit`, `apw-learn` — treated as a suite, not seven isolated prompts.
- **Central claim:** Each skill is an internally-consistent discipline that actually compensates for its stated agent limitation, and the seven cohere as a suite — consistent conventions, clean step boundaries, no contradictions, accurate cross-references and docs.
- **Verdict:** revise

## Summary

Revise — the suite is sound and the individual skills are well-reasoned, but two should-fix gaps are genuine: `apw-review` has its triage and fundamental-conflict steps in the wrong order (directly contradicting its own stated failure mode), and `apw-commit` enables model self-invocation for a skill that writes git commits without documenting that risk. Three minor/nit findings round out the review.

## Fundamental conflict

No foundational problem. The suite's premise — compensate for agent limitations with explicit workflow disciplines — is coherent, and each skill targets a real, named failure mode. The issues are structural and documentation gaps, not a wrong approach.

## Findings

| # | Finding | Lens | Location / claim | Severity | Confidence |
|---|---------|------|-----------------|----------|------------|
| 1 | `apw-review` Steps 5 and 6 are in the wrong order: Triage (Step 5) runs before Surface fundamental conflicts (Step 6), so the model assigns severity labels to nits *before* checking if the whole artifact is foundationally broken — exactly the failure Step 6 says must not happen | correctness of reasoning | `apw-review/SKILL.md` Step 6: "A review that lists ten nits while missing that the central claim is false has failed" — but Step 5 just listed all the nits | should-fix | high |
| 2 | `apw-commit` has `disable-model-invocation: false` but performs `git commit` and `git push`. In an autonomous pipeline the Step 4 confirmation gate assumes a human who can say no; only `apw-learn` explicitly designs for safe self-invocation. No documentation warns against autonomous use. | hidden assumption | `apw-commit/SKILL.md` frontmatter + Step 4 | should-fix | medium |
| 3 | `apw-brainstorm` has no "Ready to proceed?" gate before the actual idea-generation step (Step 3). Step 1 confirms the framing, but unlike every other skill in the suite there is no explicit plan-then-confirm gate before the execution starts. | cross-skill consistency | `apw-brainstorm/SKILL.md` — compare Step 3 of apw-research, apw-implement, apw-verify, apw-review which all have an explicit confirmation gate before execution | minor | high |
| 4 | Template path references are inconsistent: `apw-research` and `apw-brainstorm` cite the full repo-relative path (`.claude/skills/apw-research/RESEARCH_TEMPLATE.md`); `apw-review` and `apw-verify` use the informal phrase "in this skill's directory." An agent resolving the reference has different information depending on which skill it's running. | cross-skill consistency | `apw-research/SKILL.md` Step 7 vs `apw-review/SKILL.md` Step 7 | nit | high |
| 5 | `apw-brainstorm` and `apw-research` `description:` frontmatter overlap for "explore options/approaches" prompts. The chain order (brainstorm→research) is clear in the skill bodies but absent from the fields that drive auto-selection. | edge case / mis-selection | Both `description:` frontmatter fields — neither surfaces the upstream/downstream relationship | minor | medium |

## Strongest remaining objection

Finding #2 (auto-invocation safety) is the strongest remaining concern. `apw-commit` can be triggered by a model in an autonomous pipeline — no human in the loop — and can push to a remote. The confirmation gate (Step 4) works only when someone can actually say no. For a framework whose stated purpose is making *agentic* workflows reliable, shipping a git-writing skill with silent self-invocation enabled and no documented risk is an ironic gap. The fix is cheap (add one sentence of scope documentation or set `disable-model-invocation: true`), but the gap is real until it's there.

## Killed objections

- **`apw-learn` targets `AGENTS.md` but Claude Code uses `CLAUDE.md`:** The project's own memory records that AGENTS.md is among the always-on files Claude Code reads. Not a real problem.
- **`apw-verify` vs `apw-review` for plans — ambiguous overlap:** The `description:` fields are meaningfully different ("verify an implementation meets requirements" vs "find what's wrong with your own diff/plan/conclusion before shipping"). Overlap is acceptable design. Killed.
- **Chain order not self-describing in description fields:** UX note, not a correctness issue. The README and rationale docs cover it adequately. Killed.
- **`apw-review` Step 3 "consciously switch stance" is just an instruction to feel differently:** After looking at Step 4's explicit lenses (correctness, hidden assumptions, missed alternatives, edge cases, scope, strongest counter-argument), the structural mechanism is there. Step 3's language is imprecise but Step 4 compensates. Killed.

## Notes

- The template for `apw-review` actually handles Finding #1 correctly — the "Fundamental conflict" section appears *before* the "Findings" table in `REVIEW_TEMPLATE.md`. The fix to the SKILL.md is to swap the step order to match the template: surface fundamental conflicts during or immediately after the attack phase (Step 4), then triage the remaining findings (currently Step 5). Alternatively, merge Steps 5 and 6 into a single "Triage and surface" step that checks for fundamental problems first.
- Finding #3 (`apw-brainstorm` missing gate): this could be by design — brainstorming may intentionally skip the plan-then-confirm gate to lower the activation energy. If so, the distinction should be explicit, not accidental.
