[← All rationales](index.md)

# apw-tech-design

`/apw-tech-design "<task description>"`

A structured technical design workflow: familiarize → ask (if needed) → present options → confirm → write artifact & summarize. It sits between open-ended ideation ([apw-brainstorm](apw-brainstorm.md)) and full planning ([apw-plan](apw-plan.md)): it takes a task description and turns it into a concrete set of technical options, a recommendation, and a lightweight design artifact the user confirms before planning or implementation begins.

## The bad practice it prevents

When asked "how should we implement X?", the instinct is to pick the most familiar approach, call it the plan, and start building. It feels efficient — and it buries the decision.

**Without the skill:**

- **No options surface.** The agent commits to the first technically-viable approach without exploring alternatives — so the user never sees the tradeoffs that would have changed the choice.
- **No user input on technical direction.** The design happens silently inside the implementation, not as an explicit step the user can correct.
- **Wrong approach discovered late.** The codebase constraint that ruled out the obvious option is found halfway through implementation, not before it started.
- **Overengineered or underengineered.** Without a deliberate comparison step, the agent either reaches for a complex solution when a simple one fits, or dismisses complexity the task actually warrants.
- **Undocumented choice.** Even when the right approach is taken, nobody recorded *why* — so the decision gets relitigated next time.

## How the skill helps

It inserts a lightweight checkpoint between "task received" and "plan started":

1. **Familiarize.** Read the task, the relevant codebase, and any prior artifacts (`research.md`, `brainstorm.md`). Know what constraints are real before proposing options.
2. **Ask if needed.** Resolve genuine ambiguity — up to 5 questions — that would change which options are viable. Skip if the task is clear.
3. **Present options.** Lay out up to 3 technical options with concrete pros and cons in context, and a clear recommendation with the most important reason. One option is fine when there is no meaningful alternative.
4. **Confirm.** Ask if the full artifact should be written. Wait.
5. **Write & summarize.** Produce `tech-design.md`, then report the recommendation and any gaps honestly.

> **The core idea:** the technical approach is the most expensive thing to reverse after implementation begins. Surfacing options and getting a "yes, that one" before any plan is written costs almost nothing; discovering the wrong approach after the plan is written and code is committed costs a lot. This skill inserts a lightweight, explicit checkpoint that makes the decision visible instead of buried.

## Lightweight by design

Unlike `apw-plan`, this skill is not trying to produce a complete set of requirements, design decisions, and checkable tasks. Its job is narrower: agree the technical direction before work goes deeper. When the approach is confirmed, the artifact is compact — options considered, the chosen recommendation, gaps — not a full spec. If a deeper plan is then needed, it hands off cleanly to `apw-plan`.

The artifact is `tech-design.md` (location per [`apw-work-with-work-item-artifacts`](apw-work-with-work-item-artifacts.md)), structured by [`TECH_DESIGN_TEMPLATE.md`](../../.claude/skills/apw-tech-design/TECH_DESIGN_TEMPLATE.md): Task → Options considered → Recommendation → Gaps & open questions.

## Sample walkthrough

A team needs to add full-text search to their product catalog before building anything.

### 1 · The request

```
user: /apw-tech-design "add full-text search to the product catalog"
```

### 2 · Familiarize, then ask one question

```
assistant: Familiarized with the codebase. The app uses Postgres — no Redis,
no Elasticsearch. The catalog table has ~500k rows.

One question:
  1. Should search rank by relevance, or is a simple keyword match enough?
user: relevance ranking matters — users expect Google-like results.
```

### 3 · Present options

```
assistant: Three options:

Option 1 — Postgres full-text search (tsvector + GIN index)
  Pros: zero new infrastructure, works with existing Postgres, fine for < 1M rows
  Cons: no typo-tolerance, relevance tuning is limited beyond basics

Option 2 — Typesense (self-hosted)
  Pros: typo-tolerance, fast, relevance scoring out of the box
  Cons: one more service to run and maintain

Option 3 — Algolia (SaaS)
  Pros: best-in-class relevance, no infra to operate
  Cons: per-search cost, data leaves the cluster, vendor lock-in

Recommendation: Option 1. You have no Redis or Elasticsearch today, relevance
ranking is achievable with tsvector weights, and at 500k rows Postgres handles
it comfortably. If relevance quality proves insufficient later, Option 2 is a
clean upgrade path with no changes to the application query layer.

Should I write the full tech-design doc?
user: yes
```

### 4 · Write the artifact

```
assistant: Wrote docs/work-items/product-search/tech-design.md.

Recommendation: Postgres FTS (tsvector + GIN index) — zero new infrastructure,
covers the relevance requirement at the current catalog size.

Gap: typo-tolerance is not achievable without a third-party option. If that
requirement surfaces, re-evaluate before committing to this approach long-term.
```

A fuzzy "add search" brief became a concrete technical decision with an agreed-on approach and one honestly-named gap — ready to hand to `/apw-plan` or directly to `/apw-implement` for a well-scoped task.

---

Part of [AgenticPW](../../README.md) · See also the [apw-brainstorm rationale](apw-brainstorm.md) and [apw-plan rationale](apw-plan.md).
