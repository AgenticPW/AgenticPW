[← All rationales](index.md)

# apw-commit

`/apw-commit ["optional hint"]`

A guided commit workflow: inspect the diff, draft a message against a project schema, review what will be staged, and commit only after explicit confirmation. The history becomes an asset instead of noise.

## The bad practice it prevents

Committing is the last step before context evaporates, so it gets the least attention. The result is a git log that's useless exactly when you need it most — during a regression hunt six months later.

**Without the skill:**

- **Throwaway messages.** `"fix"`, `"wip"`, `"updates"` — they describe nothing and make `git log` and `git blame` dead ends.
- **Inconsistent format.** Everyone invents their own style, so the history can't be scanned, filtered, or fed to a changelog tool.
- **Accidental `git add -A`.** Unrelated files, debug prints, and stray secrets get swept into the commit because nobody looked at what was staged.
- **No "why".** The diff shows *what* changed; the reasoning that future-you actually needs is lost.
- **Surprise pushes.** Work lands on the remote before it was meant to, sometimes on the wrong branch.

## How the skill helps

It turns committing into a short, reviewed pipeline. Nothing touches git history until you say so.

1. **Inspect.** Run `git status` and `git diff HEAD` to see exactly what's pending — and stop early if there's nothing to commit.
2. **Apply the schema.** Read `.claude/skills/apw-commit/COMMIT_SCHEMA.md` and use it as the authoritative format. On first run it proposes a sensible default and **saves your choice** for every future commit.
3. **Draft from the diff.** Derive type, scope, a concise subject, and a body that explains *what* changed and *why* — folding in any hint you passed.
4. **Review before staging.** Show the files to be staged and the full message, then ask **"Commit these changes? Also push?"**
5. **Commit deliberately.** Stage only relevant files (prefer specific paths over `git add -A`), commit via heredoc, and push only when asked — never force-push without explicit request.
6. **Report.** Confirm the short SHA, push result, and any errors with next steps.

> **The core idea:** a commit message is documentation written at the one moment you have full context. Spend thirty seconds on it now, or spend an afternoon reconstructing it later. The schema makes that thirty seconds consistent across the whole team.

## The project schema

This repo ships a default schema. The skill enforces it so every commit reads the same way:

```
<type>(<scope>): <short summary>

<body — what changed and why, optional>

<footer — breaking changes, issue refs, co-authors, optional>
```

| Type | Use for |
|------|---------|
| `feat` | A new capability |
| `fix` | A bug fix |
| `refactor` | Restructuring without behavior change |
| `docs` · `chore` · `test` · `style` · `perf` | Docs, tooling, tests, formatting, performance |

Rules: subject ≤ 72 chars, imperative mood ("add", not "added"), body wraps at 80, issues referenced as `Closes #123`.

## Sample walkthrough

### Before — the typical commit

```
$ git add -A && git commit -m "fix stuff"
# 3 files committed — including a stray console.log and an
# unrelated .env edit nobody noticed.
```

### After — the same change through the skill

```
user: /apw-commit "the totals were off by a cent on rounding"

assistant: Pending changes:
  M  src/billing/totals.ts
  M  .env            ← unrelated, I'll leave this unstaged
  M  src/debug.ts    ← stray console.log, excluding

Proposed message:
  fix(billing): correct rounding on order totals

  Totals were summed as floats before rounding, producing off-by-
  one-cent results on some carts. Round each line item before
  summing instead.

  Closes #218

  Commit these changes? Also push to remote? (yes / no / yes+push)
user: yes+push

assistant: ✓ committed a1b9f3c · ✓ pushed origin main
  Staged only src/billing/totals.ts.
```

Same fix, but the message is searchable, the "why" is captured, the issue is closed automatically, and the unrelated `.env` and debug line stayed out of history — because someone actually looked before staging.

---

Part of [AgenticPW](../../README.md) · See also the [apw-implement rationale](apw-implement.md).
