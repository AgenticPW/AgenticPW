---
description: Build and maintain a durable codebase-orientation record in AGENTS.md so sessions stop re-deriving the codebase from scratch, and orient from it by verifying against reality first. Use when explicitly asked to onboard, to build/refresh the orientation, or to get oriented in a codebase.
disable-model-invocation: true
metadata:
  version: "1.0"
---

Use this skill to build and maintain a durable **codebase orientation** — the architecture map, entry points, build/test/run commands, and conventions a session needs to act competently — and to get oriented from it in a fresh session. It exists to fight one specific limitation: a model **re-derives its understanding of the codebase every session**, spending its opening context re-reading the same files and re-inferring the same structure, and sometimes guessing wrong. The fix is to write that orientation down once, where every future session already has it.

The orientation lives in **`AGENTS.md`** — the always-on instructions file every session reads automatically — so the map is reused across sessions for free, with no separate file to remember to open. Because `AGENTS.md` is injected into *every* session, a stale orientation is misinformation everywhere; keeping it true is therefore part of the discipline, not an afterthought.

This skill is **user-invoked only** — the agent must never reach for it on its own. Invoking it is a deliberate act, so the invocation *is* the authorization: the skill **writes the orientation to `AGENTS.md` directly** and the user reviews the result in the file (the diff), rather than approving wording in the chat first. This differs from `apw-learn`, which shares the file but proposes-then-confirms — there, the agent may self-invoke, so a chat gate is needed; here the user already chose to run it.

`apw-learn` and `apw-onboard` both write to `AGENTS.md` but own different things: `apw-learn` records **prescriptive rules** (do X, never Y); `apw-onboard` maintains a single **descriptive orientation** section (how the codebase is laid out, built, and run). Keep each in its own section — do not let the orientation accumulate rules, or the rules accumulate architecture notes.

The skill has two modes — **build** (survey and write/refresh the orientation) and **orient** (read it and verify against reality before trusting it). Determine the mode first, then follow that mode's steps strictly and in order.

## Output style

When this skill asks for a plan, report, summary, status update, or other chat-facing output, prefer bullet points or numbered lists over plain paragraphs. Keep each item concise and content-bearing. Omit language that only smooths tone, repeats context, or adds "language sugar" without changing the meaning.

## Step 1 — Determine the mode

- **Build** — create the orientation for a project that has none, or refresh one that has drifted. The deliverable is an up-to-date Codebase Orientation section in `AGENTS.md`.
- **Orient** — get up to speed at the start of a session: read the existing orientation, verify it against the current repository, and rely on it only where it still holds. The deliverable is a reality-checked understanding, plus a refresh of any stale parts found.

State which mode you are in before continuing. If asked to orient but `AGENTS.md` has no orientation section, say so plainly and switch to Build — do not invent an orientation from a single pass and present it as established.

## Build mode

### Step B1 — Survey the codebase

Gather the orientation a competent session needs, drawing on the real repository — not memory or assumption:

- **What it is** — the project's purpose in a sentence or two, and the languages and frameworks it's built on.
- **Architecture & layout** — the major components and how they fit together; the top-level directories and what each is for.
- **Entry points** — where execution starts (CLI/server bootstrap, request handlers, job entry points) and the few files a newcomer should read first.
- **Build · test · run** — the exact commands to build, run the tests, lint, and start the app locally, **confirmed** against the project's config (package scripts, Makefile, CI) rather than guessed.
- **Conventions** — the patterns this codebase actually follows (naming, layering, error handling, module organization) as observed in the code, not generic best practice the agent already knows.
- **Gotchas** — non-obvious constraints, required setup, or traps that cost a session time.

Favour **stable, slow-changing** facts. Deliberately exclude volatile detail that rots between sessions — specific line numbers, current work-in-progress, exhaustive file-by-file listings — because a wrong orientation loaded into every session is worse than none. Prefer primary sources: read the config and the code, and run the build/test commands to confirm they work, rather than trusting a README that may itself be stale.

### Step B2 — Draft the orientation

Draft the **Codebase Orientation** section using `.claude/skills/apw-onboard/ORIENTATION_TEMPLATE.md` as the structure. Keep it tight — this text is injected into every session, so every line must earn its place; prune anything a session can cheaply rediscover. Record a **freshness marker** at the top of the section: the date and the short commit SHA it was validated against (`git rev-parse --short HEAD`), so a future reader knows how stale it might be.

### Step B3 — Check the target file

Look for `AGENTS.md` in the repository root before writing:

- **If it exists:** read it. Learn its structure so the orientation lands as its own section without disturbing existing rules, and reconcile — if an orientation section already exists, you are *refreshing it in place*, not appending a second one.
- **If it does not exist:** you'll create it with a small set of logical headings.

### Step B4 — Write it

Write the section to `AGENTS.md` directly — do not stage it in the chat first:

- Place it in its own clearly-headed section (e.g. `## Codebase Orientation`); create the file if it doesn't exist.
- If refreshing, **replace** the previous orientation in place and update the freshness marker — keep it a living section, never an append-only log; history lives in version control.
- Don't duplicate `apw-learn`'s rules; if a prescriptive rule has crept into the orientation, leave it for `apw-learn` instead.

### Step B5 — Report

One or two lines: that `AGENTS.md` was created or updated, which section was written, and the validated SHA. Point the user at the file (or the diff) to review — that is where review happens. Don't restate the contents.

## Orient mode

### Step O1 — Read the orientation

Read the **Codebase Orientation** section of `AGENTS.md`, including its freshness marker — note the date and validated SHA so you know how old it is before you lean on it.

### Step O2 — Verify against reality

The orientation records what was true when it was written; the repository may have moved since. Before relying on it, check it against the current repo:

- Run `git diff --stat <validated-SHA>..HEAD` and `git log --oneline <validated-SHA>..HEAD` to see what changed since it was validated, and concentrate verification on the parts the orientation describes.
- Spot-check the load-bearing claims: do the named entry points and directories still exist? Do the build/test/run commands still work?

Where reality disagrees with the orientation, **reality wins**. Note each drift explicitly — it changes what you can trust.

### Step O3 — Refresh on drift

If the orientation is materially stale, switch to Build mode for the parts that drifted: write the correction to `AGENTS.md` directly — fixing the wrong lines and updating the freshness marker — for the user to review in the file. A small, accurate orientation kept current is the whole point; one nobody trusts because it's wrong is worse than none.

### Step O4 — Proceed

Continue the actual task with a verified understanding, relying on the orientation only where Step O2 confirmed it still holds — and through whichever skill the task itself calls for (`apw-implement`, `apw-find-root-cause`, …).
