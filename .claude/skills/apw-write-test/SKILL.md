---
description: Structured test-writing workflow. Use when asked to write new tests for a unit of behavior — in TDD (tests first) or post-hoc (tests after) mode.
disable-model-invocation: false
metadata:
  version: "1.0"
---

Use this skill when the deliverable is *new test code* — whether you are writing tests first (TDD) or covering an already-written change (post-hoc). It exists to fight one specific failure: left alone, an agent writes tests that **codify whatever the code currently does** — assertions reverse-engineered from the implementation instead of derived from intended behavior. Those tests pass by construction, are never seen to fail, exercise only the happy path, and mock away the very logic under test. Green coverage that proves nothing. This is the testing-specific face of the model's **bias toward confirming its own output**.

The antidote is structural: derive the expected behavior from the *requirement* before reasoning from the code, and trust no test until it has been *seen to fail*. That same spine works in both modes — in post-hoc mode it makes you behave as if the tests came first. This is a different job from deciding *what work to do* (`apw-plan`), proving a *finished* implementation meets its requirements (`apw-verify`, which consumes tests rather than authoring them), or general adversarial review (`apw-review`, of which Step 6 here is a narrow, test-only echo). Follow the steps below strictly and in order.

## Step 1 — Establish the behavior under test

Before writing anything, pin down what you are testing and against what:

- **The unit and its contract** — the function, module, endpoint, or component under test, and the *promise* it makes to its callers: inputs, outputs, side effects, errors. Test the contract, not the internals — tests bound to internals break on every refactor and prove nothing about behavior.
- **The source of intended behavior** — the requirement, spec, plan, or ticket that says what the unit *should* do. If your work item has a `requirements.md` / `design.md` / `plan.md` artifact (see `apw-work-with-work-item-artifacts` for where these live), read it. If there is no written spec, the intended behavior must come from the user (Step 3), not from the code.
- **The test harness and conventions** — the framework, the directory layout, naming, fixtures, and assertion style already used in this project. New tests must look like the existing ones.

Do not skip this step, even in post-hoc mode where the code already exists. The goal is a clear picture of *intended* behavior before the implementation's logic can anchor you to *actual* behavior.

## Step 2 — Derive expected behavior independently of the implementation

This is the heart of the skill and the step the bias fights hardest. **Before** reasoning from the implementation's internals, write down what *should* happen, derived from the contract and the spec in Step 1 — not from reading the code's branches and restating them.

Enumerate the cases the behavior implies:

- **Happy path** — the primary expected use, with concrete inputs and the expected result.
- **Edge cases** — boundaries, empty/zero/one, maximum, off-by-one, ordering.
- **Error cases** — invalid input, missing dependencies, failure of a called collaborator; the promised error or fallback.
- **Invariants** — properties that must hold across all inputs (idempotence, round-trip, conservation, monotonicity), when one exists.

Weight by risk — spend cases where the unit is most likely to be wrong or most costly if it is, not uniformly. If a reference oracle, property, or invariant exists, prefer it: a test that restates an independent property is far harder to make tautological than one that restates the code.

In **post-hoc mode**, do this step *before* studying the implementation's logic. The deliverable of this step is a case list traceable to intended behavior — that is what makes a post-hoc suite structurally resemble TDD instead of a mirror of the code.

## Step 3 — Present the test plan and confirm

Before writing test code, state concisely:

- **The case list** from Step 2 — grouped happy / edge / error / invariant — so the user can spot a behavior you misread or missed.
- **The levels** — what will be covered as unit vs integration tests, and what (if anything) needs real I/O, fixtures, or test doubles.
- **What deliberately won't be tested**, and why — out-of-scope behavior, things covered elsewhere, cases the user may not want spent on.

Then ask: **"Ready to proceed?"**

Wait for the user to confirm or correct the plan before writing tests. This gate matters most when there is **no written spec** — here is where the intended behavior gets agreed, rather than silently invented from the code. For a small, obvious set of tests keep the plan to a few lines — but still confirm.

## Step 4 — Write the tests

Write the agreed cases, one at a time. For each test:

- **Trace every assertion to a behavior from Step 2**, not to a line of the implementation. If you cannot say which intended behavior an assertion defends, delete it.
- **Mock only what is genuinely external** — the network, the clock, the filesystem, a third-party service. Never mock the unit under test or the logic the test exists to exercise; a test that mocks away its own subject asserts nothing.
- **Follow the project's conventions** from Step 1 — naming, structure, fixtures, assertion style.
- Keep each test focused on one behavior, so a failure names what broke.

## Step 5 — Prove each test can fail

A test never seen red is not yet trusted — it may be green by construction. Before trusting any test, see it fail for the right reason:

- **TDD mode:** run the test against the not-yet-written (or stubbed) implementation and watch it fail with the expected assertion error, *then* implement until it passes.
- **Post-hoc mode:** make each test red on purpose — invert its assertion, or temporarily break the code path it covers (a deliberate bug / mutation) — confirm it fails, then restore. A test that stays green when the behavior is broken is worthless; fix or delete it.
- **When tests cannot be run** in this environment, do not skip the gate — reason it through explicitly: for each test, state what change to the code *would* make it fail, and confirm it isn't tautological or trivially satisfiable. A test for which you cannot name a breaking change is a red flag.

Kill tautological tests (assert true, assert a mock returned what you told it to) and over-mocked tests here — they survive this gate only by accident.

## Step 6 — Adversarial self-check

Switch from author to critic and attack the suite for test-specific smells. For each test ask:

- **Tautological?** — does it assert the code does what it does, or that intended behavior holds? Would it pass against a *wrong* implementation?
- **Happy-path only?** — were the edge, error, and invariant cases from Step 2 actually written, or quietly dropped?
- **Over-mocked?** — does a mock stand in for the logic the test claims to exercise?
- **Brittle?** — will it break on a legitimate refactor that preserves behavior? It should test the contract, not the internals.
- **Coverage theater?** — does it execute lines without asserting their effect, chasing a coverage number rather than behavior?

Fix what these surface. Do not invent problems to look thorough — but a suite that clears every smell on the first pass deserves a second, harder look.

## Step 7 — Honest summary

When done, report concisely:

- **What's covered** — the behaviors and case-types now tested.
- **What's deliberately not covered**, and why.
- **The proof state** — were the tests seen to fail (run), or was the failure gate reasoned through because the suite couldn't be run here? Say which.
- **Known gaps and follow-ups** — cases worth adding later, missing fixtures, behavior that needs a real environment to test.

Be honest. The point of this skill is a suite you can *trust to fail when the code is wrong* — reporting clean coverage while edge cases went untested or the failure gate was skipped is the exact bias this skill exists to defeat.
