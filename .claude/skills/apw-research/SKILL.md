---
description: Structured research workflow. Use when a question must be investigated before it can be implemented or decided.
disable-model-invocation: false
metadata:
  version: "1.0"
---

Use this skill when asked to research, investigate, evaluate options, or understand a problem space *before* committing to an implementation. It is the upstream companion to `apw-implement`: research produces a durable artifact that an implementation can then build on. Follow the steps below strictly and in order.

## Step 1 — Frame the question

Before consulting any source, write down what the research must actually answer:

- **Core question** — the single decision this research must inform. If you cannot state it in one sentence, the task is not ready to research yet.
- **Sub-questions** — the smaller questions the core question breaks down into.
- **Why it matters** — what decision or work hangs on the answer.
- **Done-criteria** — what "enough to decide" looks like, so the research has a finish line.
- **Scope** — what is explicitly in and explicitly out.

Present this framing to the user and confirm it before going further.

## Step 2 — Ask questions and choose a strategy (only if needed)

If anything about the framing is genuinely ambiguous, ask up to 5 targeted questions that would change how the research is conducted. Skip if the framing is already clear — do not ask for the sake of asking.

In this same round, assess **parallelism**: if the sub-questions are independent and non-overlapping — different domains, sources, or codebase areas that can be investigated without one depending on another — tell the user this research could be split across several parallel agents, and **ask whether they want to spawn them**.

- If **yes**: spawn one agent per independent sub-question (e.g. `Explore` for codebase sweeps, `general-purpose` for open-ended investigation). Give each agent its sub-question, the sources to consult, and instruct it to report findings **with references** (file paths, line numbers, URLs). Synthesize their results yourself in Step 6.
- If **no**, or the sub-questions are interdependent: proceed sequentially.

## Step 3 — Plan the investigation and confirm

List, before spending time:

- **Sources** per sub-question — codebase, official docs, web/prior art, commit history, runtime behavior, the user or a domain expert.
- **Method** per sub-question — read, search, prototype/spike, benchmark, compare.
- **Order** — cheapest, highest-signal sources first.

Present the plan and ask: **"Ready to proceed?"**

Wait for the user to confirm or correct the plan before gathering evidence. This gate exists to avoid burning a session down a rabbit hole.

## Step 4 — Gather evidence

Work the plan one sub-question at a time. For every finding, capture three things:

- **Claim** — what you learned.
- **Reference** — exactly where it came from: file path + line number, a full URL, a commit SHA, or a documented command and its output. Every claim must be traceable so a reader can verify it independently.
- **Confidence** — `verified`, `likely`, or `unconfirmed`.

Track which sub-questions are answered and which are still open. Prefer **primary** sources (the actual code, the official spec) over **secondary** ones (blog posts, recollection).

## Step 5 — Verify, don't trust

Do not let an important claim rest on a single source or on memory:

- Cross-check key claims against a second source.
- Prefer reality over documentation where they can disagree — read the actual implementation, run a quick spike, or check runtime behavior when a claim is cheap to test.
- Never state facts about APIs, versions, pricing, or behavior from memory without confirming against a primary source.
- Explicitly separate **facts** from **assumptions** in your notes.

The goal is the root cause / ground truth, not a plausible-sounding answer.

## Step 6 — Surface surprises

If the evidence contradicts the framing — the core question was wrong, an assumed constraint does not exist, or the scope is larger than agreed — stop and tell the user. Re-frame with them rather than quietly answering a different question than the one they asked.

## Step 7 — Synthesize, recommend, and record

1. Organize findings around the original questions from Step 1.
2. When comparing options, lay them against the done-criteria as a small decision matrix with honest tradeoffs.
3. End with a **concrete recommendation** and the reasoning behind it. Research that does not converge to a recommendation is not finished.
4. Write the result to a durable artifact at **`docs/<task-short-slug>/research.md`** in the project root, where `<task-short-slug>` is a short kebab-case name for the task. Use `.claude/skills/apw-research/RESEARCH_TEMPLATE.md` as the structure. The artifact **must include the references collected in Step 4** so any reader can verify every claim without redoing the work. This file is what `apw-implement` Step 1 consumes.

## Step 8 — Honest summary

When done, report:

- The recommendation and its overall confidence level.
- What is **verified** vs what remains an **assumption**.
- What could not be determined, and why.
- Open questions and risks the implementation (or a follow-up research pass) must carry.
- The path to the written artifact.

Be honest. Do not present a confident answer where the evidence is thin.
