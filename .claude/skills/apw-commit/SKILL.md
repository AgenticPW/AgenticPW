---
description: Analyze pending git changes, draft a commit message using a project schema, and commit/push after user review.
disable-model-invocation: false
metadata:
  version: "1.0"
---

Use this skill when the user wants to commit (and optionally push) pending changes. Follow every step below in order.

## Step 1 — Inspect pending changes

Run the following commands to understand what will be committed:

```bash
git status
git diff HEAD
```

If there are no changes (clean working tree and staging area), tell the user there is nothing to commit and stop.

## Step 2 — Check for a commit schema

Look for the file `.claude/skills/apw-commit/COMMIT_SCHEMA.md` in the repository root.

**If it exists:** read it and use it as the authoritative format guide for all commit messages in this project. Proceed to Step 3.

**If it does not exist:**

1. Present the following suggested default schema to the user:

---
**Suggested commit message schema:**

```
<type>(<scope>): <short summary>

<body — what changed and why, optional>

<footer — breaking changes, issue refs, co-authors, optional>
```

Types: `feat`, `fix`, `refactor`, `chore`, `docs`, `test`, `style`, `perf`

Rules:
- Subject line ≤ 72 characters
- Use imperative mood ("add", not "added")
- Body wraps at 80 characters
- Reference issues as `Closes #123`
---

2. Ask the user: **"Use this schema, or provide your own format?"**
3. Wait for the user's answer. Accept either a confirmation of the default or a custom format description/template they paste in.
4. Write the agreed-upon schema to `.claude/skills/apw-commit/COMMIT_SCHEMA.md` so it is used automatically in future runs.
5. Confirm to the user that the schema has been saved.

## Step 3 — Draft the commit message

Using the schema from Step 2 as the format guide and the diff from Step 1 as the content source:

1. Identify the type, scope, and a concise subject line.
2. Write a body that explains *what* changed and *why* (skip if the subject line is self-explanatory and the diff is small).
3. Add any footer items (breaking changes, issue refs, co-authors) if relevant.
4. If the user passed any arguments to this skill (e.g., a hint like "fix for issue #42"), incorporate them.

## Step 4 — Present summary for user review

Show the user:

- The list of files that will be staged/committed (from `git status`)
- The full proposed commit message

Then ask: **"Commit these changes? Also push to remote? (yes/no/yes+push)"**

Wait for explicit confirmation before proceeding. Do not commit without it.

## Step 5 — Commit (and optionally push)

**Staging:** Stage only the files that are relevant to the changes being committed. Prefer `git add <specific-files>` over `git add -A` unless the user explicitly wants to stage everything.

**Committing:** Use a HEREDOC to pass the multi-line commit message:

```bash
git commit -m "$(cat <<'EOF'
<subject line>

<body>

<footer>
EOF
)"
```

**Pushing:** If the user asked to push, run `git push` after a successful commit. Confirm the target remote and branch before pushing (e.g., `origin main`). Do not force-push unless the user explicitly requests it.

**After each operation** run `git status` to verify the outcome and report it to the user.

## Step 6 — Report result

Tell the user:
- Whether the commit succeeded (include the short SHA)
- Whether the push succeeded (if requested)
- Any errors that occurred and what to do next
