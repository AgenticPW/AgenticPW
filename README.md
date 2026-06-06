# AgenticPW

A practical collection of agentic practices and workflows, including reusable skills, commands, reference notes, and base knowledge documents.

## Skills

The skills live in [`.claude/skills/`](.claude/skills/) and are invoked from Claude Code by name. Each one turns a tempting shortcut into a small, repeatable discipline. A deeper write-up of *why* each skill matters — the bad practice it prevents and worked examples — lives in [`docs/rationale/`](docs/rationale/index.md).

### apw-research

**How it helps** — A structured research workflow and the upstream companion to `apw-implement`. Instead of skimming one doc and reporting an opinion as fact, it forces you to *frame the question first* (with done-criteria and scope), plan which sources to consult, and gather evidence where every finding carries a **reference and a confidence grade**. Claims are cross-checked against primary sources, surprises that break the framing are raised rather than papered over, and the work converges to a concrete recommendation written to a durable artifact. When sub-questions are independent, it offers to split the work across parallel agents. The result is a verifiable findings doc that feeds straight into implementation — instead of research that evaporates and gets redone.

**How to use it** — Run `/apw-research "<question to investigate>"`. The skill walks through frame → ask & strategize → plan → confirm → gather → verify → synthesize → record, waits for your **"Ready to proceed?"** before spending time, and writes the result to `docs/<task-short-slug>/research.md` using the template at `.claude/skills/apw-research/RESEARCH_TEMPLATE.md`.

### apw-implement

**How it helps** — A structured implementation workflow. Instead of jumping straight to edits, it forces you to *familiarize first*, surface genuine ambiguity as questions, agree on a mini plan, and only then implement. Conflicts discovered mid-implementation are raised rather than worked around, and the final summary names tradeoffs and gaps instead of presenting a clean picture. The result is fewer wrong-direction rewrites and fewer surprises at review time.

**How to use it** — Run `/apw-implement "<describe the feature or fix>"`. The skill walks through familiarize → ask → plan → confirm → implement → summarize, and waits for your **"Ready to proceed?"** confirmation before writing any code.

### apw-commit

**How it helps** — A guided commit workflow. It inspects the pending diff, drafts a message against a project commit schema (`.claude/skills/apw-commit/COMMIT_SCHEMA.md`), and shows you exactly what will be staged before anything happens. Nothing is committed or pushed without explicit confirmation. The payoff is a consistent, searchable git history and no accidental commits of unrelated files.

**How to use it** — Run `/apw-commit` (optionally with a hint like `/apw-commit "fix for issue #42"`). The skill drafts a message from your diff, shows the files it will stage, and asks **"Commit these changes? Also push to remote?"** before doing anything. On first use without a schema, it offers a sensible default and saves your choice for next time.

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
