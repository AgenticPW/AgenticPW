[← All rationales](index.md)

# apw-review

`/apw-review "<the diff, plan, or conclusion to review>"`

A structured adversarial self-review: establish target → plan & confirm → switch to the adversarial stance → attack under lenses → triage → surface conflicts → record → verdict. It trades a few minutes of attacking your own work for catching the flaw before it ships instead of after.

## The bad practice it prevents

Once a model has produced something — a diff, a plan, a conclusion — it is biased toward defending it. The natural "let me double-check" becomes a search for reasons the work is *right*, not reasons it is *wrong*, and the review confirms what the author already believes. This is the same self-evaluation blind spot people have about their own writing, and it ships the flaw the author was closest to and least able to see.

**Without the skill:**

- **Rubber-stamp review.** "Looks good to me" — the work is re-read approvingly, not attacked, so nothing new is found.
- **Anchoring to the first idea.** The approach that was produced first is defended as the approach, and simpler or safer alternatives are never weighed.
- **Unexamined assumptions.** What had to be true for the work to hold is never surfaced, because the author already assumed it.
- **One-objection reviews.** The single concern that comes to mind first is noted; the edge case, the scope creep, the strongest counter-argument go unstated.
- **Nit-picking past the real problem.** A list of small fixes papers over the fact that the central claim doesn't actually hold.

## How the skill helps

It replaces the impulse to confirm with seven ordered steps built around one hard pivot: stop being the author, become the critic.

1. **Establish the target.** Name the artifact, its central claim, and what would make it *wrong* — so there is a concrete thing to attack, not a vague feeling to admire.
2. **Plan & confirm.** Present what will be reviewed and the specific points it will be reviewed against, then wait for **"Ready to proceed?"** — so the user can add an angle they care about, drop an irrelevant one, or redirect the review before any time is spent.
3. **Switch to the adversarial stance.** Drop the author's defense and adopt the frame "this is wrong — where?" Treat the work as someone else's; reward finding a real flaw, not clearing the work.
4. **Attack under lenses.** Work the confirmed angles — correctness of reasoning, hidden assumptions, missed alternatives, edge cases, scope, the strongest counter-argument — each objection tied to a specific location or claim, not a vague worry.
5. **Triage honestly.** Grade each objection by severity and confidence, and *kill the ones that don't hold up* — manufacturing problems is as dishonest as rubber-stamping.
6. **Surface fundamental conflicts.** If the work is wrong at its foundation, lead with that instead of burying it under small fixes.
7. **Record.** Write the surviving findings, the verdict, and the strongest remaining objection to a durable artifact — even when the work survives cleanly.
8. **Honest verdict.** Report ship / revise / reconsider, the blocking findings, and the single best argument against the work even if you'd ship it anyway.

> **The core idea:** the person who made the work is the worst-placed to approve it, because they already believe it. This skill manufactures the missing distance — it makes you argue *against* your own output at full strength, so the flaw you were too close to see gets caught before it ships.

This is the *reasoning* counterpart to its siblings: `apw-verify` proves the work runs by checking it against requirements (empirical), while apw-review attacks the thinking behind it — the assumptions, the logic, the road not taken — which no amount of running can surface. The two are complementary, not redundant: a change can pass every test and still rest on a wrong assumption, and a plan or a conclusion can't be "run" at all.

## Sample walkthrough

An agent has just produced a plan and is about to present it as final. Watch how the skill turns "this plan looks solid" into finding the assumption that sinks it.

### 1 · The request

```
user: /apw-review "the caching plan I just wrote — review it before I commit"
```

### 2 · Establish target, plan & confirm

```
assistant: Target: the 4-step plan to add a write-through cache to the
  user-profile service. Central claim: "this cuts read latency without
  staleness risk."

  I'll attack it on: (a) does it actually reduce latency, (b) can reads
  go stale, (c) was a simpler approach dismissed too fast, (d) concurrency
  on the cache client. Anything to add or drop?

  Ready to proceed?
user: yes, and double-check the multi-writer case — two services write profiles
```

### 3 · Switch stance, attack under lenses — and find the real problem

```
assistant: Findings:
  - hidden assumption (BLOCKING): the plan assumes a single writer.
    There are two services that write profiles; write-through from one
    leaves the other's cache stale. The central no-staleness claim fails.
  - missed alternative (should-fix): read-through with a short TTL is
    simpler and sidesteps the multi-writer problem entirely — not weighed.
  - scope (minor): step 4 adds cache metrics nobody asked for.

  Killed: I flagged a thread-safety worry on the cache client, but it's
  already synchronized — not a real problem.
```

### 4 · Record and verdict

```
assistant: Verdict: RECONSIDER — the plan's core promise (no staleness)
  doesn't hold under the second writer, which the plan never accounts for.
  Strongest objection: you're solving for latency with an approach whose
  correctness assumes something false about the system.
  Wrote docs/work-items/profile-cache/review.md with findings and the killed objection.
```

The plan "looked solid" and would have been committed. Arguing against it surfaced the single false assumption underneath the whole approach — and the review names it plainly instead of nodding the plan through.

---

Part of [AgenticPW](../../README.md) · See also the [apw-verify rationale](apw-verify.md).
