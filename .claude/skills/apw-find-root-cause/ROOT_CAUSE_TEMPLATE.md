# Root-Cause Template

Copy this structure into your work item's **`root-cause.md`** artifact — see `apw-work-with-work-item-artifacts` for where work-item artifacts live. This artifact
proves *what is wrong*, not how to fix it — the fix is handed off to
`apw-implement` / `apw-plan`. A root cause is only "proven" once its causal chain
is backed by evidence a reader can follow; otherwise report the best-supported
hypothesis and exactly what would confirm it.

```markdown
# Root cause: <the issue in one line>

- **Date:** <YYYY-MM-DD>
- **Slug:** <task-short-slug>
- **Status:** proven | best-supported (unproven)
- **Confidence:** high | medium | low

## Symptom

What is observably broken: what happens vs what's expected, where/when it
manifests, when it started, and the evidence the issue came with (errors, logs,
a failing test). State the symptom, not a theory about its cause.

## Reproduction

The exact steps and minimal conditions that trigger the failure — or, if it
could not be reproduced, what was tried and why reproduction failed (which is
itself a finding).

## Hypotheses considered

Every plausible cause weighed — including the ones ruled out, and *why*. The
rejected rows are the record that this wasn't single-track tunnel vision.

| # | Hypothesis (mechanism) | Verdict | Evidence for / against |
|---|------------------------|---------|------------------------|
| 1 | ... | confirmed | reproduced; toggling it removes the symptom |
| 2 | ... | rejected | `path/to/file.ts:42` shows X, which rules it out |
| 3 | ... | unconfirmed | plausible, but no evidence either way |

## Investigation

The steps taken to discriminate between hypotheses, and what each revealed.

| Step | What it tested | What it revealed |
|------|----------------|------------------|
| added logging + re-ran | which branch executes | confirmed path B, not A |
| bisected history | when it broke | introduced in commit `a1b9f3c` |

## Root cause & causal chain

The underlying defect, and the evidence-backed chain from cause to symptom:

> cause → intermediate effect → … → observed symptom

State the proof — ideally that toggling the cause changes the symptom — and
where it lives (`path/to/file.ts:line`). Confirm this is the *underlying* defect,
not an intermediate or contributing cause one level up.

If the chain is **not fully proven**, say so plainly: name the best-supported
hypothesis, its confidence, and the precise evidence or experiment that would
confirm it.

## What the fix must address

The hand-off to `apw-implement` / `apw-plan`. Name what a correct fix has to
resolve — the defect, the conditions it must handle, and any constraints — so the
fix targets the root, not the symptom. **Do not design the fix here.**

- ...

## Open questions & notes

Anything unresolved, surprises worth flagging (misreported symptom, external
cause, multiple independent root causes), or risks the fix should carry forward.

- ...
```
