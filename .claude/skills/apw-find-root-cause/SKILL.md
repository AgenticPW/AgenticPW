---
description: Structured root-cause diagnosis workflow. Use when a known defect or failure must be diagnosed and its cause proven before anyone attempts a fix.
disable-model-invocation: false
metadata:
  version: "1.0"
---

Use this skill when something is observably broken — a defect, a failure, a wrong result — and you need to find and *prove* its root cause before a fix is attempted. It exists to fight one specific failure: **patching the symptom, or committing to the first plausible cause without proving it**. Under pressure to resolve the issue, a model latches onto the earliest explanation that fits, then "fixes" one level above the real defect — so the symptom returns, or a workaround hides a problem that was never understood. This skill replaces the first plausible story with a proven causal chain: capture the symptom, reproduce it, hold *competing* hypotheses, and converge on the cause through an iterative, evidence-driven loop with the user.

This skill is **diagnostic-only**. It ends at a proven (or best-supported) root cause and explicitly does **not** design or apply the fix — that is handed off to `apw-implement` (optionally via `apw-plan`). It is distinct from `apw-research`: research is open-ended investigation of an unknown space, while this skill diagnoses a *known, observed* defect. It sits **upstream** of the fix — it is how you prove what is wrong before anyone changes code. Follow the steps below strictly and in order.

## Step 1 — Capture the symptom

Before forming any theory, pin down exactly what is broken — and separate the symptom from its cause:

- **What happens vs what's expected** — the precise observed behavior and the behavior it should have.
- **Where and when it manifests** — the environment, the inputs, the conditions; whether it's consistent or intermittent.
- **When it started** — a recent change, a deploy, a version bump, or "always."
- **Evidence the user already has** — error messages, stack traces, logs, screenshots, a failing test, a repro.

Write this down as the ground truth of the investigation. A cause cannot be proven against a vaguely-stated symptom. Do not theorize about *why* yet — that is Step 3.

## Step 2 — Reproduce / establish ground truth

Attempt a **reliable reproduction** of the failure — the single strongest tool you have, because it both discriminates between hypotheses and lets you later prove the causal link by toggling the cause.

- If you can reproduce it, record the exact steps and the minimal conditions that trigger it.
- If you **cannot** reproduce it, that is not a stopping point — it is itself an investigative state. Record what you tried, and make "why can't this be reproduced" part of the investigation (environment difference, missing data, timing, a condition you haven't captured).

Where a live repro isn't possible, capture the most concrete evidence available (logs, a trace, a memory dump) as the substitute ground truth.

## Step 3 — Form competing hypotheses

Enumerate **multiple plausible causes** — never a single track. For each hypothesis, state:

- **The mechanism** — how this cause would produce the observed symptom.
- **A confidence level** — `high` / `medium` / `low`, given what's known so far.
- **Supporting and contradicting evidence** — what points toward it, and what points away.

Holding competing hypotheses at once is the core discipline that fights anchoring: it stops you from confirming the first story and ignoring the rest. Even when one cause seems obvious, name at least one alternative before moving on.

## Step 4 — Run the iterative evidence loop

This is the heart of the skill — a **user-in-the-loop** cycle, repeated until the cause converges:

1. **Rank** the current hypotheses by confidence, with the evidence behind each.
2. **Propose the next discriminating step(s)** — the cheapest, highest-signal action that would most separate the hypotheses. Steps are task-dependent: add logging and re-run, inspect a value or state, request debug info the user already has, write a probe test, bisect the history, diff a working vs broken environment, read a specific code path.
3. **When several useful steps apply, offer the user a choice** rather than picking unilaterally — they often know which is cheapest or have the data already.
4. **Wait** for the user to run the step, confirm it, or supply the data. Do not assume the outcome.
5. **Incorporate** the new evidence: update each hypothesis's confidence, and **prune** the ones the evidence rules out.
6. **Repeat** until converged.

**Convergence rule:** the loop ends when either (a) one hypothesis has a *proven causal chain* (Step 5) — that is the root cause; or (b) no further evidence is practical, in which case report the **best-supported hypothesis with its confidence level and the precise evidence or experiment that would confirm it**. Never silently upgrade "likely" to "proven."

Keep the loop proportional: when the cause is already obvious and the evidence is in hand, a single iteration is enough — don't manufacture steps for a trivial bug.

## Step 5 — Prove the causal link (no fix)

Before declaring a root cause, establish an **evidence-backed chain** from cause to symptom: *cause → intermediate effect → … → observed symptom*. Two disciplines:

- **Dig to the underlying defect.** Keep asking "why" past intermediate and contributing causes. The first thing that *correlates* with the symptom is usually not the root — it's a link in the chain. Stop only at the defect that, if corrected, would prevent the symptom.
- **Demonstrate the link where feasible.** The strongest proof is showing that toggling the cause changes the symptom — reproduce it, then make the symptom disappear by neutralizing the cause (or appear by introducing it). Correlation alone is a hypothesis, not a proof.

**No fix here — designing and applying the fix is out of scope.** Name what the fix must address; do not build it.

## Step 6 — Surface conflicts & surprises

If the investigation reveals that you're answering the wrong question, stop and tell the user rather than forcing a conclusion. Raise it when:

- The **symptom is misreported** — what's actually happening differs from the brief.
- The "bug" is **correct behavior** — the expectation is wrong, not the code.
- The cause is **external or environmental** — outside the codebase you were pointed at.
- There are **multiple independent root causes**, not one.
- The scope is **materially larger** than the brief implies.

Let the user decide how to proceed. A confident answer to the wrong question is the failure this step prevents.

## Step 7 — Record the artifact

Write the diagnosis to a durable artifact at **`docs/<task-short-slug>/root-cause.md`** in the project root, where `<task-short-slug>` is a short kebab-case name for the task (reuse the slug of the issue, so the diagnosis sits alongside any related research, plan, or eventual fix). Use `.claude/skills/apw-find-root-cause/ROOT_CAUSE_TEMPLATE.md` as the structure. The artifact must contain:

- The **symptom** and the **reproduction** (or why it couldn't be reproduced).
- The **hypotheses considered** — including the rejected ones and *why* each was ruled out.
- The **investigative steps taken** and what each one revealed.
- The **proven root cause and its causal chain**, with the supporting evidence.
- The **confidence level** — and, if not fully proven, exactly what would confirm it.
- A **"what the fix must address"** hand-off section — naming the defect for the fix to target, *without designing the fix*.

This is the structured input `apw-implement` / `apw-plan` consumes. Write it even when the cause was obvious.

## Step 8 — Honest summary & hand-off

When done, report in chat — briefly, since the detail is in the artifact:

- The **root cause**, stated plainly, with its **confidence level**.
- The **key evidence** that proves the causal chain.
- If **not fully proven**: the best-supported hypothesis and exactly what remains to confirm it.
- The **path to the artifact**.
- That **fixing is the next step** — hand off to `apw-implement` (or `apw-plan` first if the fix is non-trivial).

Be honest. Do not present a proven root cause where the chain is unproven — reporting "this is the strongest hypothesis, here's what would confirm it" is a valid and necessary result. A confident answer to the wrong cause is the exact failure this skill exists to prevent.
