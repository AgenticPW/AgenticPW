---
description: Structured adversarial self-review workflow. Use when about to ship your own diff, plan, or conclusion and you want to find what's wrong with it before someone else does.
disable-model-invocation: false
metadata:
  version: "1.0"
---

Use this skill to critically review your own work — a diff, a plan, or a conclusion — *before* presenting it as final. It exists to fight one specific failure: a model is **biased toward its own output**, so once it has produced something it tends to defend and rationalize it rather than attack it, and a quick "looks good to me" ships the flaw it just authored. This skill forces the opposite posture: drop the author's defense, assume the work is flawed, and hunt for *how*. It reviews the **reasoning** — the assumptions, the missed alternatives, the weak logic, the unconsidered case — which is a different job from proving the work runs (`apw-verify`) or building it (`apw-implement`). It sits **downstream** of producing something and **upstream** of presenting it. Follow the steps below strictly and in order.

## Output style

When this skill asks for a plan, report, summary, status update, or other chat-facing output, prefer bullet points or numbered lists over plain paragraphs. Keep each item concise and content-bearing. Omit language that only smooths tone, repeats context, or adds "language sugar" without changing the meaning.

## Step 1 — Establish the review target

Before critiquing anything, pin down exactly what is under review:

- **The artifact** — the specific diff, plan, conclusion, or claim being reviewed. Name it concretely (a set of changed files, a written plan, a stated finding like "the bug is in the cache layer" or "this is done").
- **The central claim** — what the artifact is asserting is true or correct ("this change fixes X", "this is the right approach", "this conclusion follows from the evidence"). This is what you will try to break.
- **What would make it wrong** — the conditions under which the claim fails. If you cannot state how the work *could* be wrong, you cannot review it — you can only admire it.

Do not skip this step. A review with no target degrades into vague praise.

## Step 2 — Present the review plan and confirm

Before attacking anything, state concisely:

- **What will be reviewed** — the target and its central claim, so the user can correct the scope if you've aimed at the wrong thing.
- **What it will be reviewed against** — the specific lenses and points you intend to attack under (e.g. "the multi-writer assumption", "whether the simpler TTL approach was unfairly dismissed", "the empty-input path"), not just the generic lens names. This is the list the user gets to shape: they may add an angle they care about, drop one that's irrelevant, or redirect the whole review.

Then ask: **"Ready to proceed?"**

Wait for the user to confirm or adjust the points before spending time. This gate exists so the review attacks the things that actually matter to the user, not a default checklist. For a small, obvious review keep the plan to a few lines — but still confirm.

## Step 3 — Switch to the adversarial stance

This is the heart of the skill and the step the bias fights hardest. Consciously stop being the author and become the critic. The goal is **not** to confirm the work is good — it is to find the strongest reason it is *not*. Adopt the frame: "This is wrong. Where?"

A genuine switch means: treat the work as if someone else produced it and you have been asked to find the flaw they missed; reward yourself for finding a real problem, not for clearing the work. If after this step you still feel protective of the artifact, you have not switched — name that, and push harder.

## Step 4 — Attack under explicit lenses

Work through the distinct critique lenses you confirmed in Step 2 so the review covers more than the one objection that comes to mind first. For each, generate concrete objections tied to a **specific location or claim**, not a vague worry:

- **Correctness of reasoning** — does the conclusion actually follow? Is there a logical gap, a non-sequitur, a step that was assumed rather than shown?
- **Hidden assumptions** — what must be true for this to hold that was never checked? What is taken for granted about inputs, environment, or behavior?
- **Missed alternatives** — is there a simpler, safer, or more correct approach that wasn't considered? Was the first idea shipped because it was first?
- **Edge cases & failure modes** — where does this break? Empty, null, concurrent, large, malicious, or out-of-order inputs; partial failure; the unhappy path.
- **Scope** — does it do less than claimed, or more than asked (unrequested changes, gold-plating)?
- **The strongest counter-argument** — if a sharp reviewer wanted to reject this outright, what is the single best reason they would give? State it at full strength.

A vague objection ("this might have issues") is not a finding. A finding names where and why.

## Step 5 — Surface fundamental conflicts

If the review reveals that the work is not just flawed but **wrong at its foundation** — the whole approach is misguided, the conclusion is unsupported, the plan solves the wrong problem — stop and say so plainly rather than burying it under a list of small fixes. A review that lists ten nits while missing that the central claim is false has failed. Lead with the fundamental problem when there is one.

## Step 6 — Triage the findings honestly

Not every objection survives scrutiny, and inventing problems is its own failure mode. For each objection, assign:

- **Severity** — `blocking` (must fix before shipping), `should-fix` (real, address it), `minor`, or `nit`.
- **Confidence** — how sure you are it's a genuine problem versus a hunch.

Then **kill the ones that don't hold up.** Manufacturing fake problems to look rigorous is as dishonest as rubber-stamping. The output is the set of objections that survived your own pushback, ranked by severity.

## Step 7 — Record the review artifact

Write the outcome to your work item's **`review.md`** artifact — see `apw-work-with-work-item-artifacts` for where work-item artifacts live — using `.claude/skills/apw-review/REVIEW_TEMPLATE.md` as the structure. The artifact must contain:

- The **review target and central claim**.
- The **surviving findings** with severity, confidence, and the specific location/claim each attacks.
- Any **fundamental conflict** surfaced in Step 5.
- The **verdict** — ship / revise / reconsider.
- The **strongest remaining objection**, even if it didn't change the verdict.

Write the file even when the work survives review cleanly — "I attacked this and it held" is a result worth recording.

## Step 8 — Honest verdict

When done, report in chat — briefly, since the detail is in the artifact:

- The **verdict** — ship as-is / revise first / reconsider the approach.
- The **blocking and should-fix findings**, named plainly.
- The **single strongest objection** that remains, so the reader knows the best case against the work even if you judged it shippable.
- The **path to the artifact**.

Be honest. The point of reviewing your own work is to find the flaw before it ships — a review that clears everything in order to feel finished is the exact bias this skill exists to defeat.
