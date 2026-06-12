[← All rationales](index.md)

# apw-verify

`/apw-verify "<what to verify, or a path to the spec/plan/requirements>"`

A structured verification workflow: establish target → ask → plan → confirm → verify → surface conflicts → record → summarize. It trades a few minutes of checking for the difference between "I think it's done" and "here's the proof it's done."

## The bad practice it prevents

When an implementation looks finished, the natural instinct is to call it done and move on. The code reads correctly, the change is in the right place — surely it works. That confidence is exactly the problem: it is reasoning *about* the code, not evidence *from* it.

**Without the skill:**

- **Success by assumption.** "Done" is declared because the code looks right, never because it was run, tested, or checked against the requirement.
- **Vague target.** The work is verified against a fuzzy memory of what was asked instead of the actual requirements, so a missed requirement is never noticed.
- **Confabulated passes.** Each requirement is waved through on plausibility; the one that quietly fails ships anyway.
- **Silent gaps.** Things that *couldn't* be checked are simply omitted, so the report looks complete when it isn't.
- **Rosy verdict.** A clean "it all works" is reported while real failures and unverified corners go unmentioned.

## How the skill helps

It replaces the impulse to declare success with seven ordered steps, each one a checkpoint that converts a claim into evidence.

1. **Establish the target.** Read the requirements (a description or a referenced spec/plan) *and* the implementation, then turn the requirements into a concrete checklist where each item is a single checkable claim — so there is a fixed, objective definition of "done."
2. **Ask (up to 5, only if needed).** Pin down any acceptance criterion too vague to check. Skip when it's already clear.
3. **Plan & confirm.** State the checklist, how each item will be checked, and what can't be verified, then wait for **"Ready to proceed?"** — so you verify the real requirements, not a misremembered version.
4. **Verify against reality.** Check each item by running it, testing it, or reading the actual code; grade it `pass` / `fail` / `partial` / `unable-to-verify`, each backed by evidence. A pass with no evidence is a guess.
5. **Surface conflicts.** If the implementation contradicts the spec, the spec is impossible, or a problem appears outside the checklist, stop and raise it — don't pass it silently, don't silently fix it.
6. **Record.** Write verdicts, evidence, failures, open questions, and what couldn't be verified to a durable artifact — even when everything passes.
7. **Honest summary.** Report the overall verdict, the counts, the critical gaps, and the path to the artifact.

> **The core idea:** "looks done" and "is done" are different claims, and only one of them can be proven. This skill insists on the provable one — and treats "I could not verify this" as a valid, necessary result rather than a gap to paper over.

## Sample walkthrough

A developer asks to confirm a finished feature actually meets its spec. Watch how the skill keeps an optimistic "yep, looks good" from shipping a quiet failure.

### 1 · The request

```
user: /apw-verify "docs/work-items/rate-limit/spec.md — verify the rate limiter is done"
```

### 2 · Establish the target, plan, confirm

```
assistant: I read the spec and the implementation. Checklist:
  1. Rejects the 101st request in a 60s window with HTTP 429.
  2. Limit is per-API-key, not global.
  3. Resets exactly 60s after the first request.
  4. Sets a Retry-After header on rejection.

  How I'll check: hit the endpoint in a loop (1,3,4), read the
  keying logic for (2). I can't verify (3)'s exact timing without a
  clock I control — I'll flag it.

  Ready to proceed?
user: yes
```

### 3 · Verify against reality — and find the quiet failure

```
assistant: Results:
  1. pass — 101st request returned 429 (ran the loop, output attached).
  2. FAIL — limiter keys on client IP, not API key. spec wants per-key.
     limiter.ts:38 reads req.ip. Two keys behind one IP share a bucket.
  3. partial — resets, but I couldn't prove the 60s boundary precisely.
  4. pass — Retry-After: 60 present on the 429.
```

### 4 · Record and summarize honestly

```
assistant: Verdict: PARTIAL — 2 pass, 1 fail, 1 partial.
  Critical: requirement 2 (per-key limiting) is not met — it's
  per-IP, which is a different and weaker guarantee.
  Wrote docs/work-items/rate-limit/verification-result.md with evidence and the
  one item I couldn't verify (exact reset timing).
```

The feature "looked done" and would have been waved through. Checking each requirement against reality caught that it enforced the wrong limit entirely — and the report says so plainly instead of reporting a pass.

---

Part of [AgenticPW](../../README.md) · See also the [apw-implement rationale](apw-implement.md).
