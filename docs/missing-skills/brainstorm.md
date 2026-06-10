# Brainstorm: What essential skills are missing from AgenticPW?

- **Date:** 2026-06-09
- **Status:** complete
- **Slug:** missing-skills

## Challenge

Identify the *essential* skills still missing from AgenticPW — the ones whose
absence leaves a real, recurring agent failure mode with no compensating
discipline.

The repo's own thesis gives the yardstick: **every skill compensates for a
structural agent limitation.** So "what's missing" = "which limitations have no
skill pointing at them yet."

### Existing coverage (the map to find gaps against)

| Agent limitation | Covered by |
|---|---|
| Mode collapse toward the average answer | apw-brainstorm |
| Finite context / drifting attention (mental model) | apw-implement (familiarize) |
| Knowledge frozen at training cutoff | apw-research |
| No knowledge of project rules (commits) | apw-commit |
| No memory across sessions (rules) | apw-learn |
| Helpfulness bias / over-delivering | apw-scope-guard *(planned)* |

### Constraints
- **Hard:** A new skill must compensate for a *structural agent limitation*,
  not just automate a handy task. This is the repo's identity.
- **Hard:** Each skill is a small, repeatable discipline as an ordered
  `SKILL.md`, in the `apw-*` shape (frame → … → record an artifact where
  relevant).
- **Assumed:** Skills are coding-oriented (all 5 current ones are). — Allowed to
  break; user confirmed "all" (non-coding disciplines in scope).
- **Assumed:** Scope is one repo of manually-named skills. — Allowed to break;
  user confirmed "any" (meta / orchestration skills in scope).
- **Assumed:** `apw-scope-guard` is already on the roadmap, so treat it as known
  and look *beyond* it.

### Success criteria
A winning suggestion:
1. Names a distinct limitation **not** covered by the existing 5 + scope-guard.
2. Is essential, not nice-to-have — its absence causes frequent real failures.
3. Is a *discipline a skill can enforce*, not a personality trait or one-off prompt.
4. Doesn't substantially overlap an existing skill's territory.

### Re-framings
- **As-is:** "What additional skills should the collection have?"
- **Inverted (gap-first):** "What are the agent's known failure modes, and which
  have no skill?" — drives off the limitation taxonomy, not off task ideas.
- **Lifecycle-framed:** "Walk a task: request → understand → diverge → research →
  plan → build → **verify → review → commit → hand off → maintain/debug**. Where
  does the agent reliably fail with no skill to catch it?"
- **Broadened:** "Forget 'skills.' What disciplines make an agent *trustworthy*
  on real work?"

> The lifecycle re-framing did the heavy lifting: it exposed that the existing
> five all live in the **front half** of a task (think, decide, build, remember
> rules). The **back half** — prove it works, find why it broke, recover, hand
> off — is almost entirely uncovered.

## Idea log

Judgment deferred — weak ideas kept.

| # | Idea | Lens | Note |
|---|------|------|------|
| 1 | **apw-verify** — run the code, observe real behavior, prove "done" before claiming it | inversion ("never run it" → flip) | Limitation: agents declare success on unverified work; confabulate that code works. The canonical reliability failure. |
| 2 | **apw-debug** — reproduce → isolate → root-cause → fix → confirm | persona (SRE) | Limitation: agents patch *symptoms* / pattern-match a plausible fix instead of finding cause. Directly matches user's global rule: never workaround without a clear root cause. |
| 3 | **apw-review** — adversarial self-review: try to break your own diff / plan / conclusion | first-principles (self-eval bias) | Limitation: sycophancy toward own output; can't critique own work. |
| 4 | **apw-handoff** — write a resumable state doc: done / next / decisions / open threads, for the next context window | first-principles (no working memory across resets) | Limitation: in-flight task state evaporates on context reset. Distinct from apw-learn (durable *rules* vs. live *task state*). |
| 5 | **apw-safeguard** — classify an action's risk (reversibility, outward-facing), checkpoint before irreversible ones | persona (SRE / release gate) | Limitation: agents act without weighing reversibility. Partly nudged by base prompt. |
| 6 | **apw-plan / apw-decompose** — break a large multi-step effort into a tracked, living checklist | constraint addition (long horizon) | Overlaps apw-implement's mini-plan; value is at *large* / multi-session scale. |
| 7 | **apw-route** — meta-skill: given a request, pick and sequence the right apw-skills | cross-domain (medical triage) | Limitation: not knowing which discipline applies. But Claude already auto-selects via descriptions. |
| 8 | **apw-delegate** — discipline for spawning sub-agents well (scoped, decorrelated, synthesized) | SCAMPER (combine) | Brainstorm/research already embed spawn logic; medium overlap. |
| 9 | **apw-vet** — vet new dependencies / copied external code (license, security, maintenance) | persona (security eng) | Overlaps built-in security-review; narrower. |
| 10 | **apw-report / apw-communicate** — when to interrupt the human, how to summarize progress | broadened (non-coding) | Risks being a *trait*, not a crisp enforceable discipline. |
| 11 | **apw-ask / apw-clarify** — disciplined upfront clarification | broadened | Redundant: implement & research already have "ask" steps. |
| 12 | **apw-tdd** — write the failing test first, then implement to green | SCAMPER (combine implement+verify) | A strong *variant* of implement+verify rather than a new limitation. |

### Past the first cluster
The safe first cluster is **verify / review / debug** — correct and essential,
but the obvious lifecycle gaps. Forcing past it (contrarian / breaking the
*assumed* "memory only grows" and "agents are persistent = good" beliefs):

- **#13 apw-unlearn / apw-prune** — the dark-side counterpart to apw-learn:
  `AGENTS.md` rots. Rules go stale, contradict each other, or become wrong as
  the code changes. A discipline to periodically *review and prune* project
  memory. Limitation: the framework currently assumes memory only ever
  *accumulates* — no garbage collection. (lens: inversion of apw-learn)
- **#14 apw-stop / apw-abort** — recognize when to *stop and escalate* rather
  than thrash. Agents loop on a failing approach (sunk-cost / persistence bias),
  burning turns retrying variants of a broken plan. A discipline to detect
  no-progress and hand back to the human. (lens: inversion — "what guarantees
  failure?" → never giving up on a dead approach)
- **#15 apw-redteam** — broader than review: assume your *conclusion* (research
  finding, architectural decision, "it's fixed") is wrong and attack it before
  shipping. (lens: cross-domain — scientific falsification)

These three I would not have produced on the first pass; #13 and #14 in
particular name limitations the repo's current framing is blind to.

## Clusters

- **A — Closing the loop (trust in output):** #1 verify, #3 review, #15 redteam, #2 debug
- **B — Continuity & long horizon:** #4 handoff, #6 plan/decompose, #14 stop
- **C — Safety of actions:** #5 safeguard, #9 vet
- **D — Meta / orchestration:** #7 route, #8 delegate
- **E — Memory hygiene (dark side of learn):** #13 unlearn/prune
- **F — Communication:** #10 report, #11 clarify

> The mass is in clusters A and B — exactly the task's back half that the
> existing skills don't touch.

## Evaluation

Scored against the success criteria. Honest downsides.

| Idea | Pros | Cons | Meets criteria? |
|------|------|------|-----------------|
| **#1 apw-verify** | Biggest single trust gap; "done without proof" is *the* reliability failure the repo exists to fight. Highly enforceable (run, observe, show output). Zero overlap. | Per-project "how to run" varies — needs a discoverable run step (mirrors apw-commit's schema pattern). | **Yes — strongest.** |
| **#2 apw-debug** | Frequent, high-pain. Distinct from implement (build *known* thing vs. find *unknown* cause). Aligns with user's own stated principle. | Risk of bloating into a generic troubleshooting essay; must stay a tight reproduce→isolate→root-cause loop. | **Yes — strong.** |
| **#3 apw-review** | Real self-eval blind spot; cheap to run; complements verify (reasoning vs. empirical). | Overlaps built-in `code-review` and verify; must own the *self*-critique-of-reasoning niche to justify itself. | **Yes, with positioning.** |
| **#4 apw-handoff** | Fills the exact gap the repo's "context management" note gestures at. Clean separation from apw-learn. | Lower frequency (only long/multi-session tasks); some users lean on the harness's own summary. | **Yes — strong.** |
| **#5 apw-safeguard** | Prevents irreversible damage; non-coding reach. | Base prompt + harness permission modes already cover much of it; marginal value as a *skill*. | Partial. |
| **#13 apw-unlearn** | Genuinely novel; fixes a structural blind spot (memory rot). | Low frequency; only matters once `AGENTS.md` is large. Could be a *mode* of apw-learn, not its own skill. | Yes, but niche. |
| **#7 apw-route** | Lowers adoption friction. | Claude already auto-routes via skill descriptions; thin. | Weak. |
| **#14 apw-stop** | Names a real failure (thrashing). | Hard to make a crisp, enforceable discipline; borders on a trait. | Borderline. |

### Killed
- **#10 apw-report / communicate** — too close to a personality trait; no crisp
  enforceable loop. Cut.
- **#11 apw-ask / clarify** — redundant with the "ask" steps already inside
  apw-implement and apw-research. Cut.
- **#12 apw-tdd** — a strong *variant* of implement+verify, not a new
  limitation. Fold into apw-verify / apw-implement, don't make it a skill.
- **#7 apw-route** — Claude already selects skills from their descriptions; a
  router skill adds little. Cut (revisit only if the collection grows large).
- **#9 apw-vet** — overlaps the built-in `security-review`; merge the dependency
  angle into **apw-safeguard** rather than a standalone skill.
- **#6 apw-plan** as a standalone — overlaps apw-implement's planning; only
  distinct at multi-session scale, where **apw-handoff** already carries the
  state. Fold in.

## Shortlist & recommendation

The headline: **AgenticPW is strong on "doing the right thing" and nearly absent
on "proving it's done and recovering when it isn't."** The most essential
missing skills all live in the task's back half.

**Tier 1 — essential, build next (after the already-planned apw-scope-guard):**

1. **apw-verify** — *Run it and prove it before claiming done.* Compensates for
   the agent's bias to declare success on unverified work. This is the single
   biggest reliability gap in a repo whose entire premise is reliability.
   → *Next:* straight to `apw-implement`. Borrow apw-commit's pattern of a
   per-project schema (here: a discoverable "how to run / how to verify" step).

2. **apw-debug** — *Reproduce → isolate → root-cause → fix → confirm.*
   Compensates for symptom-patching. Aligns precisely with the user's own
   standing rule (no workarounds without a clear root cause), which makes it a
   natural fit for this project specifically.
   → *Next:* straight to `apw-implement`.

**Tier 2 — strong, build soon:**

3. **apw-review** — adversarial self-review of a diff/plan/conclusion, owning the
   *self-critique-of-reasoning* niche that `code-review` (mechanical) and
   `apw-verify` (empirical) don't.
   → *Next:* light `apw-research` to position it against the built-in
   `code-review` and `security-review` so it doesn't overlap, then implement.

4. **apw-handoff** — a resumable in-flight state doc for the next context
   window; the live-task counterpart to apw-learn's durable rules.
   → *Next:* straight to `apw-implement`.

**Tier 3 — worth it, lower urgency:**

5. **apw-safeguard** (absorbing the dependency-vetting angle of #9) — gate
   irreversible / outward-facing actions. Lower marginal value because the base
   prompt and harness permissions already cover part of it.

**Surprise worth a separate decision:** **apw-unlearn / memory-prune** (#13)
names a blind spot the framework's current "memory only grows" model can't see.
Likely better as a *prune mode within apw-learn* than its own skill — but the
gap is real and worth recording.

## Open questions

- **apw-verify's per-project run step:** mirror apw-commit's saved-schema
  pattern (a `RUN.md` / saved "how to verify"), or detect the run command each
  time? — needs a decision before building.
- **apw-review vs. built-ins:** is the self-critique niche distinct enough from
  `code-review` to justify a separate skill, or should it be a step inside
  apw-implement's summarize phase? — resolve with light apw-research.
- **apw-unlearn:** standalone skill vs. a prune mode of apw-learn? — product call.
- **Did an assumed constraint fall?** Partly. The strongest winners are still
  coding-oriented; the "coding-only" and "manual-invocation" assumptions
  *enabled* candidates (handoff, safeguard, route) but none of those unseated the
  lifecycle gaps found via re-framing #3. Honest read: the big gap was hiding in
  plain sight in the lifecycle, not behind the assumptions.
```