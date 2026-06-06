[← All rationales](index.md)

# apw-research

`/apw-research "<question to investigate>"`

A structured research workflow: frame → ask & strategize → plan → confirm → gather → verify → synthesize → record. It is the upstream companion to [apw-implement](apw-implement.md) — it turns a fuzzy "look into this" into a referenced, verifiable artifact that an implementation can build on.

## The bad practice it prevents

When asked to "research" something, the natural instinct is to skim one doc, read one file, form an opinion, and report it as fact. It feels thorough. It usually isn't.

**Without the skill:**

- **No defined question.** Research wanders because nobody wrote down what it was supposed to answer — so it never knows when it's done.
- **One-source answers.** A single blog post or a half-remembered API becomes "the truth," and the error isn't caught until it's built on.
- **Confirmation bias.** The investigation only looks for evidence that supports a conclusion already decided.
- **Unverifiable claims.** Findings arrive with no references, so the reader has to redo the work to trust any of it.
- **Lost findings.** The research lives in a chat transcript, evaporates, and gets redone next week.
- **No recommendation.** Endless gathering that never converges into a decision anyone can act on.

## How the skill helps

It replaces "go read about it" with eight ordered checkpoints, each catching a class of mistake before it becomes expensive.

1. **Frame the question.** State the core question, sub-questions, why it matters, done-criteria, and scope — so the research has a target and a finish line.
2. **Ask & strategize.** Surface genuine ambiguity as questions, and — if the sub-questions are independent — offer to split the work across **parallel agents**.
3. **Plan & confirm.** List sources and methods, cheapest-highest-signal first, then wait for **"Ready to proceed?"** before burning a session.
4. **Gather evidence.** Every finding carries a **claim + reference + confidence**, with primary sources preferred over secondary.
5. **Verify, don't trust.** Cross-check key claims, prefer reality over docs, test what's cheap to test, and separate facts from assumptions.
6. **Surface surprises.** If evidence breaks the framing, stop and re-frame with the user instead of quietly answering a different question.
7. **Synthesize & record.** Compare options against the criteria, commit to a concrete recommendation, and write it to `docs/<task-short-slug>/research.md`.
8. **Honest summary.** Report the recommendation, its confidence, what's verified vs assumed, and the open questions the implementation must carry.

> **The core idea:** research is only worth what someone can act on and trust. A finding without a reference is a rumor; a session of investigation without a written artifact is work you'll do twice. This skill front-loads the cheap discipline — a clear question, traceable evidence, a durable doc — so the answer survives the conversation and feeds straight into implementation.

## Verifiable by design

Two rules make the output trustworthy without re-doing it:

- **Every claim is referenced.** A file path with line number, a full URL, a commit SHA, or a command and its output — enough that a reader can check it themselves.
- **Every claim is graded.** `verified` (checked against a primary source or tested), `likely` (one credible source), or `unconfirmed` (assumption or weak source) — so confidence is never overstated.

The artifact lives at `docs/<task-short-slug>/research.md`, structured by [`RESEARCH_TEMPLATE.md`](../../.claude/skills/apw-research/RESEARCH_TEMPLATE.md): Question → Findings → Options & tradeoffs → Recommendation → Open questions → References.

## Sample walkthrough

A developer needs to pick a background-job library before building anything.

### 1 · The request

```
user: /apw-research "which job queue should we use for scheduled emails?"
```

### 2 · Frame, then offer to parallelize

```
assistant: Core question: which job queue fits scheduled, retryable email sends?
Done-criteria: supports delays + retries, works with our Postgres, < 1 new
service to run. Scope: out = real-time streaming.

These sub-questions are independent — I can run them as parallel agents:
  1. What does our codebase already use for async work?
  2. Feature/ops comparison of pg-boss vs BullMQ vs Cloud Tasks.
Spawn parallel agents for these?
user: yes
```

### 3 · Plan, confirm, gather with references

```
assistant: Plan: agent A sweeps the repo; agent B compares the three libs
against the criteria, primary sources only. Ready to proceed?
user: yes

assistant: Findings (each referenced):
  • We already run Postgres, no Redis — services/db.ts:12  [verified]
  • pg-boss uses Postgres, no extra service — github.com/timgit/pg-boss  [verified]
  • BullMQ requires Redis — docs.bullmq.io/guide/connections  [verified]
```

### 4 · Recommend, record, summarize honestly

```
assistant: Recommendation: pg-boss — meets all criteria with zero new
infrastructure. BullMQ is more powerful but needs Redis (fails the criteria).

Wrote docs/scheduled-emails/research.md (findings, matrix, references).
Confidence: high on infra fit; open question: pg-boss throughput at our
volume is unconfirmed — flag a load test before relying on it.
```

The "just pick a library" question shipped as a referenced, graded document with a clear recommendation and one honestly-named gap — ready to hand to `/apw-implement` without anyone re-checking the basics.

---

Part of [AgenticPW](../../README.md) · See also the [apw-implement rationale](apw-implement.md) and [apw-commit rationale](apw-commit.md).
