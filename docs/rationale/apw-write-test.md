[← All rationales](index.md)

# apw-write-test

`/apw-write-test "<unit or behavior to test>"`

A structured test-writing workflow: establish the contract → derive expected behavior from the spec → confirm the plan → write → prove each test can fail → self-check → summarize. It works the same whether you write tests first (TDD) or cover an already-written change (post-hoc).

## The bad practice it prevents

Asked to "add tests," the natural instinct is to read the code and write assertions that match what it does. The suite goes green on the first run, coverage climbs, and it looks done. It usually proves nothing.

**Without the skill:**

- **Mirroring the implementation.** Assertions are reverse-engineered from the code's branches, so they encode *what the code does*, not *what it should do* — a bug in the code becomes a "passing" test that locks the bug in.
- **Never seen to fail.** A test that has only ever been green might be green by construction. If it can't fail, it can't catch a regression.
- **Happy-path only.** The one obvious case gets tested; the edges, errors, and invariants — where bugs actually live — are quietly skipped.
- **Mocking away the subject.** The logic under test is replaced by a mock, so the test asserts that the mock returned what it was told to. It exercises nothing.
- **Coverage theater.** Lines execute without their effects being asserted, chasing a number instead of behavior.

The common root is the model's **bias toward confirming its own output** — pointed at tests, it produces green that proves nothing.

## How the skill helps

It replaces "read the code, assert what it does" with seven ordered steps built around two mechanisms: **derive behavior from the spec before reading the implementation**, and **trust no test until it has been seen to fail.**

1. **Establish the behavior under test.** Identify the unit's *contract* and the requirement that defines intended behavior — and the existing test harness, so new tests fit in.
2. **Derive expected behavior independently.** Before reasoning from the code, enumerate happy / edge / error / invariant cases from the spec, weighted by risk. In post-hoc mode this happens *before* studying the implementation — that's what stops the mirroring.
3. **Confirm the plan.** Present the case list, the levels, and what won't be tested, then wait for **"Ready to proceed?"** — the gate where intended behavior gets agreed, not invented.
4. **Write the tests.** One case at a time, every assertion traceable to a behavior — not a line of code; mock only what's genuinely external.
5. **Prove each test can fail.** See it red for the right reason — run it (TDD: before implementing; post-hoc: invert the assertion or break the code), or, when tests can't be run, reason out exactly what change would break each one.
6. **Adversarial self-check.** Attack the suite for test-specific smells: tautological, happy-path-only, over-mocked, brittle, coverage theater.
7. **Honest summary.** What's covered, what isn't, whether the failure gate was run or reasoned, and the known gaps.

> **The core idea:** the goal isn't tests that pass — it's a suite you can *trust to fail when the code is wrong*. Deriving behavior from the spec and seeing every test go red are the two cheap checks that separate the two.

## Sample walkthrough

A developer asks for tests on a discount calculator that's already written.

### 1 · The request

```
user: /apw-write-test "cover applyDiscount() in pricing.ts"
```

### 2 · Derive behavior from the spec, not the code

```
assistant: The spec says applyDiscount(price, code) returns the
discounted price, rejects expired or unknown codes, and never returns
a negative price. Before reading the implementation, the cases:

  happy    valid 20% code on $50 → $40
  edge     100% code → $0 (boundary, not negative)
  error    expired code → throws ExpiredCode
  error    unknown code → throws UnknownCode
  invariant result is never < 0 for any input

  Out of scope: currency formatting (covered in format.test.ts).
  Ready to proceed?
user: yes — also stacking two codes should throw.
```

### 3 · Write, then prove each test can fail

```
assistant: Wrote the six tests. Proving the gate (suite runs here):

  - Inverted the $0 boundary assertion → fails as expected, restored.
  - The "never negative" test stayed GREEN even after I forced a
    negative return — it was asserting on a mocked rateTable instead
    of the real clamp. That test was worthless; rewrote it to call
    the real function. Now it goes red when the clamp is removed.
```

### 4 · Self-check and finish honestly

```
assistant: Self-check caught one brittle test asserting the internal
rounding helper was called — replaced with an assertion on the
returned value, so a refactor won't break it.

Summary
  • 7 tests: 1 happy, 1 edge, 2 error, 1 invariant, 1 stacking (yours).
  • All seen to fail for the right reason, then pass (suite ran).
  • Not covered: concurrency on the shared rateTable — needs a real
    store; recommend a follow-up integration test.
```

The expired-code and negative-price cases — the ones a mirror-of-the-code suite skips — are exactly where the real bugs would have hidden. Deriving from the spec found them; the failure gate caught a test that was green for the wrong reason.

---

Part of [AgenticPW](../../README.md) · It writes the tests that [apw-verify](apw-verify.md) later consumes when proving an implementation is done, and Step 6 is a narrow, test-only echo of [apw-review](apw-review.md). [apw-implement](apw-implement.md) may reach for this skill when a change needs tests, but the two stay decoupled.
