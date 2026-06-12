# Review Template

Copy this structure into your work item's **`review.md`** artifact — see `apw-work-with-work-item-artifacts` for where work-item artifacts live. This is an
*adversarial self-review*: the job is to find what is wrong with your own work,
not to confirm it. Every finding must name a specific location or claim and why
it is a problem — a vague worry is not a finding. Record the review even when the
work survives cleanly.

```markdown
# Review: <what was reviewed>

- **Date:** <YYYY-MM-DD>
- **Slug:** <task-short-slug>
- **Target:** <the diff / plan / conclusion under review — name it concretely>
- **Central claim:** <what the work asserts is true or correct — what you tried to break>
- **Verdict:** ship | revise | reconsider

## Summary

One or two sentences: does the work hold up? Lead with the verdict and the
headline (e.g. "Revise — sound approach, but one blocking edge case and two
should-fix gaps" or "Reconsider — the central claim doesn't follow").

## Fundamental conflict

If the work is wrong at its foundation — wrong approach, unsupported conclusion,
solving the wrong problem — state it here, plainly, before the detail. Delete this
section if there is no foundational problem.

- ...

## Findings

The objections that survived your own pushback, ranked by severity. Each names a
specific location/claim and why it's a problem.

| # | Finding | Lens | Location / claim | Severity | Confidence |
|---|---------|------|------------------|----------|------------|
| 1 | ... | correctness | `path/to/file.ts:42` assumes X, but Y | blocking | high |
| 2 | ... | hidden assumption | plan step 3 takes Z for granted | should-fix | medium |
| 3 | ... | edge case | empty-input path not handled | minor | high |

> Severity: **blocking** (must fix before shipping), **should-fix** (real,
> address it), **minor**, **nit**. Lenses: correctness of reasoning, hidden
> assumptions, missed alternatives, edge cases & failure modes, scope, strongest
> counter-argument.

## Strongest remaining objection

The single best case against the work, stated at full strength — even if you
judged it shippable anyway. So the reader knows the best argument against it.

- ...

## Killed objections

Objections you raised and then discarded under scrutiny, with why — so the review
shows its work and isn't padded with manufactured problems.

- ...

## Notes

Anything else worth flagging: follow-up, things deferred, or context the next
person should know.

- ...
```
