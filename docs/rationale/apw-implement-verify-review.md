[← All rationales](index.md)

# apw-implement-verify-review

`/apw-implement-verify-review "<task description, or an existing work-item slug>"`

A **command**, not a skill. It runs an entire straightforward task end to end by orchestrating one subagent per phase — implement, verify, review — looping until verification passes and bringing the review findings back to you for a decision on each one. Each phase runs in its own fresh context, so the work is built, proven, and attacked by three separate vantage points instead of one.

## The bad practice it prevents

Running implement → verify → review yourself, in a single conversation, quietly defeats the point of having three steps.

**Without the command:**

- **The reviewer is the author.** The same context that wrote the code reviews it — and a model is biased toward its own output, so the review rationalizes the work instead of attacking it. The flaw it just authored gets a "looks good to me."
- **Verification gets skipped or rushed.** Once the code "looks done," it's tempting to declare victory. The verify step collapses into "it should work."
- **The loop is never closed.** Verification finds gaps, but with attention already drifting toward the next thing, the gaps don't reliably get fed back into another implementation pass.
- **Findings vanish into a wall of text.** A review surfaces ten issues; without a structured decision point, half are silently dropped and the rest are fixed without you ever choosing what mattered.
- **Symptoms get patched.** A review finding gets a quick workaround instead of a diagnosis, because stopping to find the real cause feels like a detour.

## How the command helps

It assigns each phase to a **dedicated subagent that wraps the matching skill** and runs in its own context, and it makes the orchestrator — the only party that talks to you — responsible for routing results and decisions.

| Phase | Subagent | Wraps |
|---|---|---|
| Implement | `apw-implementer` | [`apw-implement`](apw-implement.md) |
| Verify | `apw-verifier` | [`apw-verify`](apw-verify.md) |
| Review | `apw-reviewer` | [`apw-review`](apw-review.md) |
| Diagnose (on request) | `apw-root-cause-finder` | [`apw-find-root-cause`](apw-find-root-cause.md) |

1. **Separate contexts beat author bias.** The reviewer never wrote the code; it sees the diff cold, the way a second engineer would. Verification is a distinct pass with its own evidence, not the implementer grading its own homework.
2. **The verify⇄implement loop is closed for you.** Verification failures are fed straight back to the implementer as the next round's scope, and re-verified — repeating until it passes, with a safety cap (3 rounds) that escalates to you instead of looping forever.
3. **You decide every review finding.** The reviewer returns ranked findings, each with a suggested fix. You choose per item: accept the fix, send it to the root-cause-finder to be **diagnosed first**, or skip it. Nothing is fixed before you've chosen.
4. **Root cause on demand.** When a finding's real cause is unclear, one choice routes it through a proper diagnosis before any code changes — so the fix targets the defect, not the symptom.
5. **Questions always reach you.** Subagents can't talk to you directly, so any genuine blocker a subagent hits is surfaced by the orchestrator and relayed back as a decision — never guessed, never silently worked around.
6. **Durable artifacts tie it together.** Every phase reads and writes the work item's artifacts (`plan.md`, `verification-result.md`, `review.md`, `root-cause.md`) under `docs/work-items/<slug>/`, so the handoff between agents survives context loss and the trail is inspectable afterward.

> **The core idea:** the value of implement / verify / review comes from them being *independent*. Collapse them into one context and the independence is lost — the author reviews itself, verification turns into optimism, and findings evaporate. This command keeps them separate and makes you the judge at the points that need judgment.

## When to use it — and when not

Use it for a **straightforward task** you want carried all the way to a reviewed, verified result without driving each phase by hand. For a genuinely complex or fuzzy task, do the upstream thinking first — [`apw-research`](apw-research.md), [`apw-brainstorm`](apw-brainstorm.md), [`apw-plan`](apw-plan.md) — and let the resulting `plan.md` feed this command. For a bug whose cause isn't yet proven, diagnose with [`apw-find-root-cause`](apw-find-root-cause.md) first; the command will pick up the `root-cause.md`.

## Sample walkthrough

```
user: /apw-implement-verify-review "add a --json flag to the status command"
```

```
assistant (orchestrator):
  Work item: status-json-flag

  → spawning apw-implementer …
  ← COMPLETED. Added --json flag; status command now renders via a
    serializer. Files: cli/status.ts, lib/serialize.ts.

  → spawning apw-verifier …
  ← VERDICT: partial (3 pass / 1 fail). FAIL: --json still prints the
    human header line to stdout, breaking machine parsing.

  → spawning apw-implementer (scope: the failing item) …
  ← COMPLETED. Header now goes to stderr in --json mode.

  → spawning apw-verifier …
  ← VERDICT: pass (4/4). Artifact: …/verification-result.md

  → spawning apw-reviewer …
  ← VERDICT: revise. Findings:
    1. [should-fix] serialize.ts swallows errors as "{}" — a failure
       looks like an empty-but-valid result.
    2. [nit] flag help text omits that --json implies --quiet.
```

```
assistant: Verification passed (4/4). The review raised two items —
how should I handle each?

  1. Error swallowed as "{}"  → suggested fix: surface as {"error": …}, exit 1
  2. Help text omits implication → suggested fix: one-line help edit
```

```
user: fix #2 with the suggestion. for #1 find the root cause first —
      I don't think serialize.ts is even where the error is caught.
```

```
assistant:
  → spawning apw-root-cause-finder (symptom: errors become "{}") …
  ← ROOT CAUSE (high): the catch is in cli/status.ts, not serialize.ts;
    it logs nothing and falls through to printing the empty accumulator.
    Fix must address the status.ts catch, not the serializer.

  Root cause differs from the reviewer's guess. Apply: in status.ts,
  emit {"error": message} to stdout and exit non-zero. Proceed?
user: yes
  → spawning apw-implementer (fixes: #1 in status.ts, #2 help text) …
  → re-verify … VERDICT: pass.

  Summary: --json flag shipped, verified 4/4. Review: #2 fixed as
  suggested; #1 diagnosed (cause was status.ts, not serialize.ts) then
  fixed. Artifacts in docs/work-items/status-json-flag/.
```

The review caught a real bug the author's context would have waved through, *you* decided what to do with each finding, and the one finding whose cause was uncertain got diagnosed — revealing the reviewer's suggested location was wrong — before a line was changed.

---

Part of [AgenticPW](../../README.md). The command is Claude-Code-specific (a slash command plus subagents); the four skills it orchestrates work in any tool that follows the Agent Skills standard.
