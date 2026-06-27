---
description: Structured divergent-then-convergent ideation workflow. Use when asked to brainstorm, ideate, generate options, or explore approaches before narrowing to a decision.
disable-model-invocation: false
metadata:
  version: "1.0"
---

Use this skill when asked to brainstorm, ideate, generate options, or explore approaches *before* committing to one. It exists to fight one specific failure: a model defaults to its single highest-probability answer, so an unaided "give me ideas" produces a few near-identical, generic options, anchored to the first framing and presented uncritically. This skill forces genuine divergence, defers judgment, then converges honestly. It sits **upstream** of `apw-research` (verify the chosen direction) and `apw-implement` (build it). Follow the steps below strictly and in order.

## Output style

When this skill asks for a plan, report, summary, status update, or other chat-facing output, prefer bullet points or numbered lists over plain paragraphs. Keep each item concise and content-bearing. Omit language that only smooths tone, repeats context, or adds "language sugar" without changing the meaning.

## Step 1 — Frame and re-frame the challenge

Before generating a single idea, write down what the ideation is actually for:

- **Goal** — what a successful outcome would achieve, in one sentence.
- **Constraints** — what is genuinely fixed, separated from what is merely *assumed* fixed. Mark each as **hard** or **assumed**.
- **Success criteria** — what a great idea would have to do to win, so convergence later has something to measure against.
- **Re-framings** — restate the problem **2–3 different ways** (e.g. broaden it, narrow it, invert it). This deliberately breaks the anchor to the user's first phrasing before any ideating begins.

Present this framing — including the re-framings — and confirm it before going further.

## Step 2 — Ask questions and set a divergence strategy (only if needed)

If anything about the framing is genuinely ambiguous, ask up to 5 targeted questions that would change the *search space* the ideation explores. Skip if the framing is already clear — do not ask for the sake of asking.

In the same round, decide the divergence strategy:

- **Lenses** — pick the idea-generation lenses you will apply in Step 3 so ideas don't all cluster (see the list there).
- **Parallelism** — independent agents generate **decorrelated** ideas, which is the strongest structural defense against mode collapse. If the challenge has separable angles (different user segments, domains, or sub-problems), tell the user the divergence could be split across **parallel agents** and **ask whether to spawn them**. If yes, give each agent the framing, a distinct lens or sub-angle, and instruct it to return raw ideas (no self-censoring). Synthesize them yourself in Step 5.

## Step 3 — Diverge: generate widely, defer judgment

Generate ideas under an explicit discipline. **No evaluation in this step** — wild, half-formed, and "bad" ideas are all welcome; judging now is what collapses the output.

- **Quantity target.** Commit to a number up front (default: aim for at least 10 genuinely distinct ideas). Quantity is what forces you past the obvious ones.
- **Apply distinct lenses** so ideas span the space instead of clustering. Use several of:
  - **First principles** — ignore how it's normally done; build up from what's actually true.
  - **Cross-domain analogy** — how does another field solve the structurally similar problem?
  - **Inversion** — "what would *guarantee* failure?", then flip each answer.
  - **Constraint removal** — "if budget / time / a hard rule weren't real, then…"
  - **Constraint addition** — "if you had one hour / one dollar / one file…"
  - **Persona shift** — how would a different role, user, or competitor approach it?
  - **SCAMPER** — substitute, combine, adapt, modify, put to other use, eliminate, reverse.
- **Capture each idea** with the lens that produced it and a one-line description.
- **Collapse duplicates.** If two ideas are the same idea reworded, merge them and push for a genuinely different one in its place. Reworded duplicates are mode collapse wearing a disguise.

## Step 4 — Push past the first cluster

This is the core anti-mode-collapse gate. Stop and inspect the batch from Step 3:

- If the ideas are all variations on the safe, default answer, say so out loud and **force several more** that are contrarian, uncomfortable, or that break one of the **assumed** constraints from Step 1.
- The deliverable of this step is at least a few ideas that you would *not* have produced on the first pass. If every idea still sits in the obvious region, the divergence isn't done.

## Step 5 — Converge: cluster and evaluate

Now, and only now, turn on judgment:

1. **Cluster** the ideas into themes so the shape of the space is visible, not a flat list.
2. **Evaluate** the strongest candidates against the success criteria from Step 1 in a small matrix with **honest tradeoffs**.
3. **Kill weak ideas explicitly.** Name the ones that don't survive and why. Presenting every idea as viable is sycophancy — the convergence is where you earn trust by cutting.

The full idea log, clustering, and evaluation matrix belong in the **artifact** (Step 7), not in chat. Do this work in service of the doc; don't paste it into the conversation.

## Step 6 — Surface surprises and challenge the framing

If the ideation reveals that the real problem is different from the framing — the goal was wrong, a "hard" constraint dissolved, or the interesting question is elsewhere — stop and tell the user. Re-frame with them rather than brainstorming the wrong problem to completion.

## Step 7 — Recommend, shortlist, and record

1. Converge to a **ranked shortlist** or a single **recommendation** with the reasoning behind it. Thirty ideas and no direction is not a finished brainstorm.
2. Say what each shortlisted idea would need *next* — typically a hand-off to `apw-research` to verify feasibility, or straight to `apw-implement` if the path is clear.
3. Write the result to your work item's **`brainstorm.md`** artifact — see `apw-work-with-work-item-artifacts` for where work-item artifacts live — using `.claude/skills/apw-brainstorm/BRAINSTORM_TEMPLATE.md` as the structure. Capturing the idea log (not just the winner) means the discarded options can be revisited instead of regenerated.

This artifact is the **single detailed record** — the full idea log, clusters, matrix, and shortlist live here.
Risks live in the separate `Risks` section.
Unresolved questions live in the separate `Open questions` section.
The chat (Step 8) gets a concise summary that points to it, never a second copy of the same content.

## Step 8 — Concise summary

The detail is already in the artifact, so keep the chat result short — its job is to tell the user the headline and send them to the doc, not to reproduce it. Report only:

- The **top recommendation** (or the 2–3 leading shortlist items) in a line or two each.
- A one-line note that weaker ideas were considered and cut, and whether any **assumed** constraint fell.
- The **path to the artifact**.
- A note that the artifact contains a separate `Risks` section.
- A note that the artifact contains a separate `Open questions` section.

Do not paste the idea log, the clustering, or the evaluation matrix into chat — that is the duplication this step exists to avoid. Stay honest: if the real options are narrow, say so in the one line rather than padding the summary to look richer than the doc.
