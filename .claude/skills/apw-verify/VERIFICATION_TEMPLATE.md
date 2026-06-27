# Verification Template

Copy this structure into your work item's **`verification-result.md`** artifact — see `apw-work-with-work-item-artifacts` for where work-item artifacts live. Every
`pass` must rest on evidence a reader can follow — a command and its output, a
test result, a file path with line number, or an observed behavior. A verdict
with no evidence is a guess, not a verification.

```markdown
# Verification: <what was verified>

- **Date:** <YYYY-MM-DD>
- **Slug:** <task-short-slug>
- **Verified against:** <description / path to spec, plan, or requirements>
- **Overall verdict:** pass | fail | partial

## Summary

One or two sentences: does the implementation meet its requirements? Lead with
the verdict and the count (e.g. "7 of 9 requirements pass; 1 fail, 1 not
verified").

## Requirements checklist

For each requirement: how it was checked, the verdict, and the proof.

| # | Requirement | How checked | Verdict | Evidence |
|---|-------------|-------------|---------|----------|
| 1 | ... | ran `cmd` | pass | output: `...` |
| 2 | ... | read code | fail | `path/to/file.ts:42` does X, spec wants Y |
| 3 | ... | ran tests | partial | passes for A, not handled for B |
| 4 | ... | — | unable-to-verify | no way to exercise this without prod creds |

> Verdicts: **pass** (proven by evidence), **fail** (proven not to meet the
> requirement), **partial** (meets some of it), **unable-to-verify** (could not
> be checked — say why).

## Failures & gaps

What does not meet the requirements, described plainly enough to act on.

- ...

## Open questions

Anything unresolved, or that needs a decision before this can be called done.

- ...

## Not verified

Requirements that could not be checked, and why — so the gap is visible rather
than hidden behind silence.

- ...

## Risks

Risks found outside the checklist, or risks created by anything not verified.

- ...

## Notes

Anything else worth flagging: surprises or follow-up the next person should know
about.

- ...
```
