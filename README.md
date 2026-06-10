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
| Finite context & drifting attention | Builds on a wrong mental model of the code | **apw-implement** — familiarize before editing |
| Knowledge frozen at a training cutoff | Reports stale or guessed facts as truth | **apw-research** — referenced, verified evidence |
| No knowledge of your project's rules | Inconsistent commits, broken conventions | **apw-commit** — encodes your commit schema |
| No memory across sessions | Repeats past mistakes; lessons learned evaporate when the context window resets | **apw-learn** — distill durable lessons into project memory |
| Declares success on unverified work | Reports "done" from code that *looks* right, never proven | **apw-verify** — check against requirements, record the evidence |
| Bias toward its own output | Reviews its own work to confirm it, not to break it — and approves the flaw it just authored | **apw-review** — switch from author to critic, attack the work, record the verdict |
| Helpfulness bias toward over-delivering | Silently expands scope, gold-plates, and does unrequested work | **apw-scope-guard** — do exactly what's asked, flag the rest *(planned)*  |

The last one is a limitation AgenticPW now recognizes but doesn't yet cover — the skill that compensates for it is on the roadmap.

## Quick Start

The fastest way to feel the difference: grab the `apw-implement` skill from this repo and start using it instead of just saying "implement X" to the agent. It takes 3 minutes to set up, but the change in output quality is enormous.

## Skills

The skills live in [`.claude/skills/`](.claude/skills/) and are invoked from Claude Code by name. Each one turns a tempting shortcut into a small, repeatable discipline. A deeper write-up of *why* each skill matters — the bad practice it prevents and worked examples — lives in [`docs/rationale/`](docs/rationale/index.md).

### apw-brainstorm

**How it helps** — A structured ideation workflow and the upstream companion to `apw-research`. Asked to "brainstorm," an agent tends to collapse onto its single most-probable answer and hand back a few generic, near-identical ideas, anchored to your first framing and all presented as great. This skill forces the opposite: it *re-frames the problem several ways* to break the anchor, generates widely under multiple distinct lenses (first-principles, inversion, analogy, constraint shifts) with judgment deferred, then explicitly **pushes past the safe first cluster** before turning on judgment to cluster, score against criteria, and kill the weak ideas. When the challenge has separable angles, it offers to spawn parallel agents so the ideas come back decorrelated. The result is a genuinely varied set of options that narrows to a ranked shortlist — not the average answer dressed up as a list.

**How to use it** — Run `/apw-brainstorm "<challenge to explore>"`. The skill walks through frame & re-frame → ask & strategize → diverge → push past the first cluster → converge → recommend → record, waits for your confirmation on the framing before generating, and writes the full idea log to `docs/<task-short-slug>/brainstorm.md` using the template at `.claude/skills/apw-brainstorm/BRAINSTORM_TEMPLATE.md`.

### apw-research

**How it helps** — A structured research workflow and the upstream companion to `apw-implement`. Instead of skimming one doc and reporting an opinion as fact, it forces you to *frame the question first* (with done-criteria and scope), plan which sources to consult, and gather evidence where every finding carries a **reference and a confidence grade**. Claims are cross-checked against primary sources, surprises that break the framing are raised rather than papered over, and the work converges to a concrete recommendation written to a durable artifact. When sub-questions are independent, it offers to split the work across parallel agents. The result is a verifiable findings doc that feeds straight into implementation — instead of research that evaporates and gets redone.

**How to use it** — Run `/apw-research "<question to investigate>"`. The skill walks through frame → ask & strategize → plan → confirm → gather → verify → synthesize → record, waits for your **"Ready to proceed?"** before spending time, and writes the result to `docs/<task-short-slug>/research.md` using the template at `.claude/skills/apw-research/RESEARCH_TEMPLATE.md`.

### apw-implement

**How it helps** — A structured implementation workflow. Instead of jumping straight to edits, it forces you to *familiarize first*, surface genuine ambiguity as questions, agree on a mini plan, and only then implement. Conflicts discovered mid-implementation are raised rather than worked around, and the final summary names tradeoffs and gaps instead of presenting a clean picture. The result is fewer wrong-direction rewrites and fewer surprises at review time.

**How to use it** — Run `/apw-implement "<describe the feature or fix>"`. The skill walks through familiarize → ask → plan → confirm → implement → summarize, and waits for your **"Ready to proceed?"** confirmation before writing any code.

### apw-verify

**How it helps** — A structured verification workflow and the downstream counterpart to `apw-implement`. Left alone, an agent tends to declare work "done" because the code *looks* right — reporting success it never actually proved. This skill replaces "it should work" with evidence: it reads the requirements (a description or a referenced spec/plan), turns them into a concrete checklist where each item is a single checkable claim, then verifies each one against reality — running it, testing it, or reading the actual implementation rather than assuming. Every requirement gets an honest verdict (`pass` / `fail` / `partial` / `unable-to-verify`) backed by evidence, conflicts between the implementation and the spec are surfaced instead of papered over, and the full result — including what *couldn't* be verified and why — is written to a durable artifact. The result is a trustworthy answer to "is this actually done?" instead of an optimistic guess.

**How to use it** — Run `/apw-verify "<what to verify, or a path to the spec/plan/requirements>"`. The skill walks through establish target → ask → plan → confirm → verify → surface conflicts → record → summarize, waits for your **"Ready to proceed?"** before spending time, and writes the result to `docs/<task-short-slug>/verification-result.md` using the template at `.claude/skills/apw-verify/VERIFICATION_TEMPLATE.md`.

### apw-review

**How it helps** — A structured adversarial self-review workflow and the reasoning counterpart to `apw-verify`. Once an agent has produced something — a diff, a plan, a conclusion — it is biased toward defending it, so a quick self-review searches for reasons the work is *right* and ships the flaw the author was closest to and least able to see. This skill forces the opposite posture: it pins down the artifact's central claim, **presents what it will attack and the points to review against for your sign-off**, then consciously switches from author to critic and hunts for *how the work is wrong* under explicit lenses (correctness of reasoning, hidden assumptions, missed alternatives, edge cases, scope, the strongest counter-argument). It triages findings by severity and confidence — and honestly **kills the objections that don't hold up**, since manufacturing problems is as dishonest as rubber-stamping — leading with any flaw that's fundamental rather than burying it under nits. Where `apw-verify` proves the work *runs* against its requirements (empirical), apw-review attacks the *thinking* behind it (which no amount of running can surface). The result is the flaw caught before it ships, not after.

**How to use it** — Run `/apw-review "<the diff, plan, or conclusion to review>"`. The skill walks through establish target → plan & confirm → switch stance → attack under lenses → triage → surface conflicts → record → verdict, presents the review scope and points and waits for your **"Ready to proceed?"** before attacking, and writes the result to `docs/<task-short-slug>/review.md` using the template at `.claude/skills/apw-review/REVIEW_TEMPLATE.md`.

### apw-commit

**How it helps** — A guided commit workflow. It inspects the pending diff, drafts a message against a project commit schema (`.claude/skills/apw-commit/COMMIT_SCHEMA.md`), and shows you exactly what will be staged before anything happens. Nothing is committed or pushed without explicit confirmation. The payoff is a consistent, searchable git history and no accidental commits of unrelated files.

**How to use it** — Run `/apw-commit` (optionally with a hint like `/apw-commit "fix for issue #42"`). The skill drafts a message from your diff, shows the files it will stage, and asks **"Commit these changes? Also push to remote?"** before doing anything. On first use without a schema, it offers a sensible default and saves your choice for next time.

### apw-learn

**How it helps** — A workflow for giving the project a memory. An agent forgets everything between sessions, so conventions it discovered, mistakes you corrected, and preferences you stated all evaporate when the context window resets — and the next session re-learns them the hard way. This skill distills those lessons into `AGENTS.md`, the always-on file every future session reads. It works from three sources: an *existing conversation* it mines for durable rules, *input you provide* in a fresh chat, or *something it figured out on its own* mid-task. Crucially, it never writes silently: it proposes the exact text and section, explains why each rule earns its place, and edits the file **only after you confirm** — which both keeps `AGENTS.md` a curated set of real rules instead of a junk drawer, and makes the skill safe to let the agent invoke by itself.

**How to use it** — Run `/apw-learn` inside a chat to mine it for lessons, or `/apw-learn "<rule to record>"` in a fresh chat to record one directly. The agent may also reach for it on its own when it learns something worth keeping. Either way it walks through determine source → gather candidates → check `AGENTS.md` → propose → confirm → apply, and asks before writing anything.

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
