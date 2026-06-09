---
description: Structured verification workflow. Use when asked to verify that an implementation meets its requirements, spec, or plan, and to record the result.
disable-model-invocation: false
metadata:
  version: "1.0"
---

Use this skill when asked to verify that an implementation is actually done — that it meets a description, spec, plan, or set of requirements. It exists to fight one specific failure: a model tends to **declare success on unverified work**, reasoning that code *looks* correct instead of checking that it *is*. This skill replaces "it should work" with evidence: derive concrete requirements, check each against reality, grade it honestly, and record the result in a durable artifact. It sits **downstream** of implementation — it is how you prove the work before claiming it. Follow the steps below strictly and in order.

## Step 1 — Establish the verification target

Before checking anything, pin down exactly what you are verifying against:

- **The requirements** — read the description provided, or the referenced spec / plan / requirements doc(s) in full. This is the source of truth for "done."
- **The implementation** — read the code, change, or artifact that is claimed to satisfy those requirements.
- **The checklist** — turn the requirements into a concrete, itemized list where **each item is a single checkable claim** ("does X happen when Y"). Vague requirements that cannot be objectively checked get flagged here rather than silently passed.

Do not skip this step. You cannot verify against a requirement you have not turned into something checkable.

## Step 2 — Ask questions (up to 5, only if needed)

If a requirement is too ambiguous to check — if "done" for some item is genuinely undefined — ask up to 5 targeted questions that would change what counts as a pass. Skip this step entirely if the acceptance criteria are already clear. Do not ask for the sake of asking.

## Step 3 — Present the verification plan and confirm

State, concisely:

- The **checklist** of requirements to verify.
- **How each item will be checked** — read the code, run it, run tests, inspect output, compare against the spec.
- Anything you expect you **will not be able to verify**, and why.

Then ask: **"Ready to proceed?"**

Wait for the user to confirm or correct the plan before spending time. This gate exists so you verify the right requirements, not a misremembered version of them. For a small, obvious verification, keep the plan to a few lines — but still confirm.

## Step 4 — Verify against reality, item by item

Work the checklist one item at a time. For every requirement, capture three things:

- **Verdict** — `pass`, `fail`, `partial`, or `unable-to-verify`.
- **Evidence** — exactly what proves the verdict: a command and its observed output, a test result, a file path + line number, an observed behavior. Every pass must rest on evidence, not on the code looking plausible.
- **Notes** — what is missing for a `partial`, why something is `unable-to-verify`, or how a `fail` manifests.

Discipline: **prefer observing reality over assuming it.** Run it, test it, or read the actual implementation rather than reasoning that it ought to work. A requirement marked `pass` with no evidence is not verified — it is guessed.

## Step 5 — Surface conflicts and surprises

If verification reveals something that breaks the assignment, stop and tell the user rather than papering over it:

- The implementation **contradicts** the spec.
- The spec itself is **wrong, contradictory, or impossible** to satisfy as written.
- You find a real problem **outside the checklist** (a regression, a security or data risk).

Do not silently mark such things as pass, and do not silently fix them — verifying and fixing are different jobs. Report, and let the user decide.

## Step 6 — Record the result artifact

Write the outcome to a durable artifact at **`docs/<task-short-slug>/verification-result.md`** in the project root, where `<task-short-slug>` is a short kebab-case name for the task (reuse the slug of the work being verified, so the result sits alongside any related research or plan). Use `VERIFICATION_TEMPLATE.md` in this skill's directory as the structure. The artifact must contain:

- The **per-requirement verdicts** with their evidence.
- **Failures and gaps** — what does not meet the requirements.
- **Open questions** — anything unresolved or needing a decision.
- **Not verified** — anything you were unable to check, and why.
- **Anything else worth flagging** — surprises, risks, or follow-up the next person should know about.

This file is the record of what was and was not proven. Write it even when everything passes.

## Step 7 — Honest summary

When done, report in chat — briefly, since the detail is in the artifact:

- The **overall verdict** — does the implementation meet its requirements? Pass / fail / partial.
- A **count** — how many requirements passed, failed, were partial, or could not be verified.
- The **critical failures and gaps**, named plainly.
- The **path to the artifact**.

Be honest. Do not report a pass if anything material is unproven or broken. Stating that something was not verified is a valid and necessary result — a clean picture that isn't true is the exact failure this skill exists to prevent.
