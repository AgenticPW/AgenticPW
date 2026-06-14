# AgenticPW

**Agentic Practices & Workflows** — a framework that makes coding agents reliable by designing *around* their limits instead of pretending they don't exist.

## The premise

A coding agent is powerful but constrained. It works inside a finite **context window**, its **attention** drifts across long inputs, it **collapses toward its most-probable answer** when asked to think broadly, it doesn't know your **project's rules and conventions**, and its world **knowledge is frozen at a training cutoff**. Most failures people blame on "the model being dumb" are really one of these limits leaking into the output — an answer guessed instead of looked up, a convention silently broken, a plan built on a misread of the codebase.

AgenticPW treats those limits as the design constraint. Each skill is a small, repeatable discipline that compensates for a specific weakness: gather context before acting, anchor claims in references instead of memory, follow *this* project's rules instead of generic defaults.

## How this differs from other collections

Most "awesome agent" repos are grab-bags of prompts and tips that assume the agent just needs cleverer instructions. AgenticPW starts from the opposite premise — the agent is capable *but constrained*, and reliability comes from workflows engineered around those constraints:

| Agent limitation | What goes wrong |
|---|---|
| Loses the thread on multi-step work | Starts coding before the work is broken down; steps get skipped and the plan drifts from the original ask |
| Finite context & drifting attention | Builds on a wrong mental model of the code |
| Knowledge frozen at a training cutoff | Reports stale or guessed facts as truth |
| Writes tests that confirm the code, not the spec | Reverse-engineers assertions from the implementation — green coverage that passes by construction, never seen to fail, and locks in whatever bug the code already has |
| Declares success on unverified work | Reports "done" from code that *looks* right, never proven |
| Bias toward its own output | Reviews its own work to confirm it, not to break it — and approves the flaw it just authored |
| Mode collapse toward the average answer | "Brainstorming" yields a few generic, near-identical ideas |
| Commits to the first viable technical approach | Picks the obvious solution without presenting options — user never weighs in on technical direction before implementation begins |
| Commits to the first plausible cause of a failure | Patches the symptom / commits to the first plausible cause without proving it, so the fix resurfaces or hides an unproven defect |
| No knowledge of your project's rules | Inconsistent commits, broken conventions |
| No memory across sessions | Repeats past mistakes; lessons learned evaporate when the context window resets |

## Quick Start

The fastest way to feel the difference: grab the `apw-implement` skill from this repo and start using it instead of just saying "implement X" to the agent. It takes 3 minutes to set up, but the change in output quality is enormous.

## Skills

The skills live in [`.claude/skills/`](.claude/skills/) and are invoked from Claude Code by name. Each one turns a tempting shortcut into a small, repeatable discipline. A deeper write-up of *why* each skill matters — the bad practice it prevents and worked examples — lives in [`docs/rationale/`](docs/rationale/index.md).

- **[apw-plan](docs/rationale/apw-plan.md)** — Turn a fuzzy brief into durable planning docs at the depth the task warrants, with every task tracing back to what it implements.
- **[apw-implement](docs/rationale/apw-implement.md)** — Familiarize, ask, agree a mini plan, then implement — surfacing conflicts and naming tradeoffs instead of coding the wrong thing.
- **[apw-write-test](docs/rationale/apw-write-test.md)** — Derive expected behavior from the spec before reading the code, then trust no test until it's been seen to fail — so the suite catches a broken implementation instead of mirroring it.
- **[apw-research](docs/rationale/apw-research.md)** — Frame the question, gather referenced evidence, verify it, and record a recommendation that feeds straight into implementation.
- **[apw-verify](docs/rationale/apw-verify.md)** — Turn requirements into a checklist and verify each item against reality with evidence, so "done" is proven, not assumed.
- **[apw-review](docs/rationale/apw-review.md)** — Switch from author to critic and attack your own diff, plan, or conclusion under explicit lenses, so the flaw you're too close to see gets caught before it ships.
- **[apw-brainstorm](docs/rationale/apw-brainstorm.md)** — Re-frame the problem, diverge under multiple lenses, push past the safe first cluster, then converge to a ranked shortlist.
- **[apw-tech-design](docs/rationale/apw-tech-design.md)** — Present up to 3 technical options with pros/cons and a recommendation, confirm with the user, then write a compact design artifact — so the technical direction is agreed before planning or implementation begins.
- **[apw-find-root-cause](docs/rationale/apw-find-root-cause.md)** — Capture the symptom, reproduce it, hold competing hypotheses, then prove the causal chain to the underlying defect before any fix.
- **[apw-commit](docs/rationale/apw-commit.md)** — Draft a commit message from the diff against a project schema and show what will be staged before anything happens.
- **[apw-onboard](docs/rationale/apw-onboard.md)** — Survey the codebase once into an `AGENTS.md` orientation section — architecture, entry points, build/test/run, conventions — and orient from it later by verifying it against reality first, so sessions stop re-deriving the codebase and a stale map gets caught, not believed.
- **[apw-handoff](docs/rationale/apw-handoff.md)** — Checkpoint a task's in-flight state (done/next, decisions, dead-ends) to the work item, and resume from it in a fresh session after verifying it against reality.
- **[apw-learn](docs/rationale/apw-learn.md)** — Distill a durable lesson into `AGENTS.md` — but only after you confirm — so the project stops re-learning the same things every session.
- **[apw-work-with-work-item-artifacts](docs/rationale/apw-work-with-work-item-artifacts.md)** — A reference skill, not a workflow: the single source of truth for where a work item's artifacts live.

---

Every skill is self-documenting in its `SKILL.md` file — open it to see each step. To customize behavior, edit that file directly; the change takes effect the next time the skill runs.

## Commands

Where a skill is a single discipline, a **command** composes several of them into one multi-agent workflow. Commands live in [`.claude/commands/`](.claude/commands/) and the subagents they drive live in [`.claude/agents/`](.claude/agents/). Unlike the skills, commands and subagents are **Claude-Code-specific** — they are not part of the cross-tool Agent Skills standard.

- **[apw-implement-verify-review](docs/rationale/apw-implement-verify-review.md)** — Carry a straightforward task all the way to a reviewed, verified result. The command orchestrates one subagent per phase, each in its own fresh context:

  | Phase | Subagent | Wraps skill |
  |---|---|---|
  | Implement | `apw-implementer` | `apw-implement` |
  | Verify | `apw-verifier` | `apw-verify` |
  | Review | `apw-reviewer` | `apw-review` |
  | Diagnose (on request) | `apw-root-cause-finder` | `apw-find-root-cause` |

  The verify⇄implement loop runs until verification passes (capped, then escalates to you), the review is run by an agent that didn't write the code, and every review finding comes back to you to **accept the fix, skip it, or send it for root-cause diagnosis first**. Any question a subagent hits is surfaced to you, never guessed. Because each agent wraps a skill that produces a durable artifact, the phases hand off through the work item's files under `docs/work-items/<slug>/`.

- **[apw-implement-review](docs/rationale/apw-implement-review.md)** — The verification-free sibling of the above, for tasks with **nothing concrete to verify against** (a trivial change, or one with no spec/plan/reproduction to check the result against). Same `apw-implementer` and `apw-reviewer` subagents in separate contexts — implement → review, with findings brought back to you to accept, skip, or diagnose first — but no verify phase, so a hollow verification isn't faked just to have one. When the task *does* have a checkable target, use `apw-implement-verify-review` instead.

## Using these skills in other tools (Cursor, GitHub Copilot, etc.)

These skills follow the [Agent Skills open standard](https://agentskills.io), which is supported by Cursor, VS Code, GitHub Copilot, OpenAI Codex, Gemini CLI, and others. To make them available in those tools, either rename the `.claude` folder to `.agents`, or symlink the skills directory:

```bash
# Option 1 — rename (if you're not using Claude Code)
mv .claude .agents

# Option 2 — symlink (keeps both Claude Code and other tools happy)
mkdir -p .agents
ln -s ../.claude/skills .agents/skills
```

After that, tools that follow the standard will discover the skills automatically. In Cursor you can invoke them with `/skill-name` in Agent chat; in VS Code Copilot they appear when the agent decides they're relevant.

> The `.agents/skills/` path is the cross-tool standard. Claude Code reads `.claude/skills/` directly and does not need the symlink.
