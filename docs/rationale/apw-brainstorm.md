[← All rationales](index.md)

# apw-brainstorm

`/apw-brainstorm "<challenge to explore>"`

A structured ideation workflow: frame & re-frame → ask & strategize → diverge → push past the first cluster → converge → surface surprises → recommend → summarize. It sits **upstream** of [apw-research](apw-research.md) — it turns a fuzzy "give me ideas" into a wide, deliberately varied set of options that narrows to a shortlist worth investigating.

## The bad practice it prevents

When asked to brainstorm, the natural instinct is to list the first handful of ideas that come to mind and call it a day. It feels generative. It usually isn't — because of how a language model produces text.

A model is biased toward its single highest-probability continuation. Left unaided, "brainstorm some options" triggers **mode collapse**: the output converges on the one safe, average answer instead of spreading across the space of possibilities.

**Without the skill:**

- **Reworded duplicates.** Five "different" ideas turn out to be the same idea phrased five ways — the list looks varied but isn't.
- **Anchoring.** Every idea stays inside the user's original framing; the premise is never questioned, so a better problem nearby is never seen.
- **Generic clichés.** The ideas are the training-data average for the topic, not anything specific to *this* situation or its real constraints.
- **No judgment.** Everything is presented as a great idea. Nothing is killed, so the list is long and the signal is low (sycophancy).
- **No direction.** A pile of options with no convergence — the user still has to do the hard part alone.

## How the skill helps

It replaces "list some ideas" with eight ordered steps that first force genuine spread, then narrow honestly. Each step targets one way ideation collapses.

1. **Frame & re-frame.** State the goal, separate **hard** from **assumed** constraints, set success criteria — then restate the problem 2–3 ways to break the anchor before any ideating.
2. **Ask & strategize.** Surface genuine ambiguity, choose the idea-generation lenses, and — the structural fix for mode collapse — offer to split divergence across **parallel agents** whose ideas are decorrelated.
3. **Diverge.** Hit a quantity target under multiple distinct lenses (first-principles, analogy, inversion, constraint add/remove, persona, SCAMPER), with judgment deferred and reworded duplicates collapsed.
4. **Push past the first cluster.** A named checkpoint: notice when every idea is a variation on the safe default, and force several that are contrarian or break an assumed constraint.
5. **Converge.** Cluster into themes, score the strongest against the criteria, and **kill weak ideas explicitly** instead of flattering all of them.
6. **Surface surprises.** If the real problem turns out to be different, stop and re-frame rather than finishing the wrong brainstorm.
7. **Recommend & record.** Converge to a ranked shortlist, say what each option needs next, and write the full idea log to `docs/<task-short-slug>/brainstorm.md`.
8. **Honest summary.** Report the shortlist, what was killed, which assumptions fell, and what each survivor needs next.

> **The core idea:** a model's default is to give you its *average* answer, confidently. Brainstorming is the one task where the average answer is exactly the wrong one. This skill spends cheap structure — re-framings, forced lenses, a quantity target, a "push past the obvious" gate — to pull the output away from the high-probability center, and only then turns on judgment to narrow it back down.

## Built to fight mode collapse

Three mechanisms make this skill different from a generic "brainstorm 10 ideas" prompt:

- **Forced lenses + a quantity target** (Step 3) make the model approach the problem from angles it wouldn't reach by following its most-probable path.
- **The "push past the first cluster" gate** (Step 4) treats the first batch as the *starting* point, not the answer — and demands ideas that break a stated assumption.
- **Optional parallel agents** (Step 2) generate genuinely decorrelated ideas, the strongest structural defense against everything collapsing onto one answer.

The artifact lives at `docs/<task-short-slug>/brainstorm.md`, structured by [`BRAINSTORM_TEMPLATE.md`](../../.claude/skills/apw-brainstorm/BRAINSTORM_TEMPLATE.md): Challenge & re-framings → Idea log by lens → Clusters → Evaluation → Shortlist → Open questions. It keeps the *discarded* ideas too, so they can be revisited instead of regenerated.

## Sample walkthrough

A team wants ideas for reducing new-user drop-off before building anything.

### 1 · The request

```
user: /apw-brainstorm "how do we get more new users to finish onboarding?"
```

### 2 · Frame, re-frame, break the anchor

```
assistant: Goal: more new users reach first value.
Hard: can't change the signup auth flow this quarter.
Assumed (worth challenging): "onboarding" = the current 5-step wizard.
Success: a great idea lifts completion without adding signup friction.

Re-framings:
  • Broaden: how do users reach first value at all — wizard or not?
  • Invert: what in the wizard actively pushes people to quit?
  • Narrow: which single step loses the most people?
Confirm this framing?
user: yes
```

### 3 · Diverge, then push past the obvious

```
assistant: First batch clustered around "make the wizard shorter" —
that's the safe default. Pushing past it (challenging the assumed
"onboarding = wizard" constraint):
  • Delete the wizard; teach inside the product on first real action.
  • Pre-fill the wizard from the signup data we already have.
  • Inversion lens: a "skip setup" path — measure if skippers convert better.
  • Analogy (games): a sandbox first session, setup deferred until needed.
```

### 4 · Converge, kill, recommend, record

```
assistant: Shortlist vs. criteria:
  1. In-product contextual onboarding — highest lift, needs research on effort.
  2. Pre-fill from signup — cheap, smaller lift, ship now.
Killed: "add a progress bar" (cosmetic, doesn't address why people leave).

Wrote docs/onboarding-dropoff/brainstorm.md (full idea log + matrix).
Next: hand #1 to /apw-research to size the build; #2 is ready for
/apw-implement.
```

A vague "get more users through onboarding" became a varied set of options — including one that questioned the wizard itself — narrowed to two ranked bets with clear next steps, while the cosmetic non-answer was named and cut.

---

Part of [AgenticPW](../../README.md) · See also the [apw-research rationale](apw-research.md) and [apw-implement rationale](apw-implement.md).
