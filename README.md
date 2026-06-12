# AgenticPW

**Agentic Practices & Workflows** — a framework that makes coding agents reliable by designing *around* their limits instead of pretending they don't exist.

## The premise

A coding agent is powerful but constrained. It works inside a finite **context window**, its **attention** drifts across long inputs, it **collapses toward its most-probable answer** when asked to think broadly, it doesn't know your **project's rules and conventions**, and its world **knowledge is frozen at a training cutoff**. Most failures people blame on "the model being dumb" are really one of these limits leaking into the output — an answer guessed instead of looked up, a convention silently broken, a plan built on a misread of the codebase.

AgenticPW treats those limits as the design constraint. Each skill is a small, repeatable discipline that compensates for a specific weakness: gather context before acting, anchor claims in references instead of memory, follow *this* project's rules instead of generic defaults.

## How this differs from other collections

Most "awesome agent" repos are grab-bags of prompts and tips that assume the agent just needs cleverer instructions. AgenticPW starts from the opposite premise — the agent is capable *but constrained*, and reliability comes from workflows engineered around those constraints:

| Agent limitation | What goes wrong | The practice that compensates |
|---|---|---|
| Mode collapse toward the average answer | "Brainstorming" yields a few generic, near-identical ideas | **apw-brainstorm** — deliberate diverge-then-converge |
| Loses the thread on multi-step work | Starts coding before the work is broken down; steps get skipped and the plan drifts from the original ask | **apw-plan** — break the task into a referenced, checkable plan first |
| Commits to the first plausible cause of a failure | Patches the symptom / commits to the first plausible cause without proving it, so the fix resurfaces or hides an unproven defect | **apw-find-root-cause** — prove the causal chain before fixing |
| Finite context & drifting attention | Builds on a wrong mental model of the code | **apw-implement** — familiarize before editing |
| Knowledge frozen at a training cutoff | Reports stale or guessed facts as truth | **apw-research** — referenced, verified evidence |
| No knowledge of your project's rules | Inconsistent commits, broken conventions | **apw-commit** — encodes your commit schema |
| No memory across sessions | Repeats past mistakes; lessons learned evaporate when the context window resets | **apw-learn** — distill durable lessons into project memory |
| Declares success on unverified work | Reports "done" from code that *looks* right, never proven | **apw-verify** — check against requirements, record the evidence |
| Bias toward its own output | Reviews its own work to confirm it, not to break it — and approves the flaw it just authored | **apw-review** — switch from author to critic, attack the work, record the verdict |
| Restates shared conventions everywhere | A convention like *where artifacts live* is duplicated across skills, templates, and docs, so the paths drift and are laborious to change | **apw-work-with-work-item-artifacts** — one canonical source of truth for where a work item's artifacts live |

## Quick Start

The fastest way to feel the difference: grab the `apw-implement` skill from this repo and start using it instead of just saying "implement X" to the agent. It takes 3 minutes to set up, but the change in output quality is enormous.

## Skills

The skills live in [`.claude/skills/`](.claude/skills/) and are invoked from Claude Code by name. Each one turns a tempting shortcut into a small, repeatable discipline. A deeper write-up of *why* each skill matters — the bad practice it prevents and worked examples — lives in [`docs/rationale/`](docs/rationale/index.md).

- **[apw-brainstorm](docs/rationale/apw-brainstorm.md)** — Re-frame the problem, diverge under multiple lenses, push past the safe first cluster, then converge to a ranked shortlist.
- **[apw-research](docs/rationale/apw-research.md)** — Frame the question, gather referenced evidence, verify it, and record a recommendation that feeds straight into implementation.
- **[apw-plan](docs/rationale/apw-plan.md)** — Turn a fuzzy brief into durable planning docs at the depth the task warrants, with every task tracing back to what it implements.
- **[apw-find-root-cause](docs/rationale/apw-find-root-cause.md)** — Capture the symptom, reproduce it, hold competing hypotheses, then prove the causal chain to the underlying defect before any fix.
- **[apw-implement](docs/rationale/apw-implement.md)** — Familiarize, ask, agree a mini plan, then implement — surfacing conflicts and naming tradeoffs instead of coding the wrong thing.
- **[apw-verify](docs/rationale/apw-verify.md)** — Turn requirements into a checklist and verify each item against reality with evidence, so "done" is proven, not assumed.
- **[apw-review](docs/rationale/apw-review.md)** — Switch from author to critic and attack your own diff, plan, or conclusion under explicit lenses, so the flaw you're too close to see gets caught before it ships.
- **[apw-commit](docs/rationale/apw-commit.md)** — Draft a commit message from the diff against a project schema and show what will be staged before anything happens.
- **[apw-learn](docs/rationale/apw-learn.md)** — Distill a durable lesson into `AGENTS.md` — but only after you confirm — so the project stops re-learning the same things every session.
- **[apw-work-with-work-item-artifacts](docs/rationale/apw-work-with-work-item-artifacts.md)** — A reference skill, not a workflow: the single source of truth for where a work item's artifacts live.

---

Every skill is self-documenting in its `SKILL.md` file — open it to see each step. To customize behavior, edit that file directly; the change takes effect the next time the skill runs.

## Using these skills in other tools (Cursor, GitHub Copilot, etc.)

These skills follow the [Agent Skills open standard](https://agentskills.io), which is supported by Cursor, VS Code, GitHub Copilot, OpenAI Codex, Gemini CLI, and others. To make them available in those tools, symlink the skills directory into the location those tools check:

```bash
# From the repo root — makes skills available to Cursor, VS Code, and other Agent Skills-compatible tools
mkdir -p .agents
ln -s ../.claude/skills .agents/skills
```

After that, tools that follow the standard will discover the skills automatically. In Cursor you can invoke them with `/skill-name` in Agent chat; in VS Code Copilot they appear when the agent decides they're relevant.

> The `.agents/skills/` path is the cross-tool standard. Claude Code reads `.claude/skills/` directly and does not need the symlink.
