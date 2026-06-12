[← All rationales](index.md)

# apw-find-root-cause

`/apw-find-root-cause "<the observed defect or failure to diagnose>"`

A structured root-cause diagnosis workflow: capture symptom → reproduce → competing hypotheses → iterative evidence loop → prove the causal link → surface conflicts → record → hand off. It is **diagnostic-only** — it proves *what is wrong* and stops there, handing the fix to `apw-implement`. It trades a few rounds of evidence-gathering for the difference between "this is probably it" and a proven causal chain.

## The bad practice it prevents

When something is broken, the pressure is to make it work again *now*. The first explanation that fits the symptom feels like the answer, so a fix gets aimed at it — usually one level above the real defect. The symptom goes quiet, everyone moves on, and it comes back; or a workaround papers over a problem nobody actually understood.

**Without the skill:**

- **Symptom patching.** The fix targets what's visible — the error, the bad value — not the underlying defect that produced it, so the problem resurfaces in a new form.
- **First-cause anchoring.** The earliest plausible story is latched onto, and every later observation is read as confirming it; the real cause is never considered.
- **Single-track tunnel vision.** One hypothesis is chased to the exclusion of alternatives that the evidence would have favored.
- **Correlation mistaken for proof.** Something that *coincides* with the symptom is declared the cause without ever showing it actually drives the symptom.
- **Confident wrong answer.** "Likely" is quietly upgraded to "proven," and a fix is built on a cause that was never established.

## How the skill helps

It replaces the rush to a plausible cause with eight ordered steps, each one a checkpoint that converts a guess into evidence — and, crucially, an iterative loop that keeps the user steering.

1. **Capture the symptom.** Pin the exact observed failure — what happens vs what's expected, where/when, since when, and the evidence in hand — and separate symptom from cause up front.
2. **Reproduce / establish ground truth.** Get a reliable repro, the strongest discriminator there is; if you can't, treat *why not* as part of the investigation rather than a dead end.
3. **Form competing hypotheses.** Enumerate multiple causes, each with a mechanism and a confidence level — never a single track — so anchoring has nothing to latch onto.
4. **Run the iterative evidence loop.** Rank the hypotheses, propose the next discriminating step (logs, a probe test, a bisect, inspecting a value), **offer a choice when several apply**, wait for the user's data, incorporate it, prune, and repeat until it converges.
5. **Prove the causal link.** Establish an evidence-backed chain from cause to symptom, dig past intermediate causes to the underlying defect, and where feasible show that toggling the cause changes the symptom. No fix here — out of scope.
6. **Surface conflicts.** Stop and raise it when the symptom is misreported, the "bug" is correct behavior, the cause is external, or there's more than one root cause.
7. **Record.** Write the symptom, hypotheses (including rejected ones and why), the steps and what each revealed, the proven chain, the confidence, and a "what the fix must address" hand-off to a durable artifact.
8. **Honest summary & hand-off.** Report the cause, its confidence, the key evidence, and that fixing is the next step — or, if unproven, the best hypothesis and exactly what would confirm it.

> **The core idea:** the first explanation that fits a symptom is a hypothesis, not a diagnosis. This skill refuses to confuse the two — it holds competing causes, converges on one through evidence the user helps gather, and proves the causal chain before anyone touches a fix. "I can't prove it yet, but here's what would" is a valid result; a confident fix aimed at the wrong cause is not.

## Sample walkthrough

A developer reports an intermittent failure. Watch how the loop keeps the obvious-but-wrong first cause from becoming the fix.

### 1 · The request

```
user: /apw-find-root-cause "checkout occasionally returns 500, can't reproduce reliably"
```

### 2 · Symptom, repro attempt, competing hypotheses

```
assistant: Symptom: ~1% of checkouts return 500, no pattern by user or amount.
  Started ~3 days ago per the logs. I can't reproduce on demand yet.

  Competing hypotheses:
    H1 (medium) — DB connection pool exhaustion under load.
    H2 (medium) — a race in the inventory-decrement path.
    H3 (low)    — a bad deploy 3 days ago introduced a null case.

  Cheapest discriminators — your call:
    (a) grep the 500s for a common stack trace,
    (b) check pool saturation metrics for those timestamps,
    (c) diff what shipped 3 days ago.
  Which do you have handiest?
```

### 3 · The loop converges — past the first plausible cause

```
user: here are 5 stack traces from the 500s
assistant: All five fault in inventory.decrement() on a null lock — that rules
  out H1 (no pool errors in any trace) and downgrades H3. H2 is now high.
  But the null lock is the *symptom one level up*, not the root.

  Next step: I want to see the lock-acquisition path. Can you confirm whether
  acquireLock() can return before the row exists for new SKUs?
user: ...yeah, for SKUs created today acquireLock returns null instead of waiting
assistant: That's the chain. Proven it by reproducing against a fresh SKU:
  new SKU → acquireLock() returns null (no row yet) → decrement() derefs null → 500.
  Toggling it: pre-seeding the lock row makes the 500 disappear every time.
```

### 4 · Record and hand off honestly

```
assistant: Root cause (confidence: high): acquireLock() returns null for SKUs
  with no lock row yet, instead of creating/waiting — decrement() then derefs it.
  The 1% are checkouts hitting same-day SKUs.

  Wrote docs/work-items/checkout-500/root-cause.md — hypotheses (incl. the pool theory I
  ruled out), the repro, the causal chain, and what the fix must address
  (lock-row lifecycle for new SKUs). Fixing is the next step — hand to
  /apw-implement.
```

The "probably the connection pool" first guess would have produced a fix that did nothing. Holding competing hypotheses and proving the chain caught a race one level below the visible null — and the diagnosis stops cleanly at the cause, leaving the fix to the next step.

---

Part of [AgenticPW](../../README.md) · The diagnostic step before the [apw-implement rationale](apw-implement.md); distinct from open-ended [apw-research](apw-research.md) (research investigates an unknown space — this proves the cause of a known defect).
