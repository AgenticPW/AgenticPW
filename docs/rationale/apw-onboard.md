[← All rationales](index.md)

# apw-onboard

`/apw-onboard` (build or refresh the orientation) · `/apw-onboard "orient"` (orient in a fresh session)

A codebase-orientation workflow with two modes: **build** — survey the repository and write a durable orientation (architecture, entry points, build/test/run commands, conventions) into a `Codebase Orientation` section of `AGENTS.md` — and **orient** — read that section in a fresh session, verify it against the current repo, and rely on it only where it still holds. It trades one deliberate survey for not re-deriving the codebase at the start of every session. It is **user-invoked only**: the agent never runs it on its own, so an invocation always means the orientation genuinely needs to be created, refreshed, or trusted.

## The bad practice it prevents

Every session starts cold. The agent re-reads the same directories, re-infers the same structure, and re-guesses the same commands — burning its opening context on rediscovery before it can do the actual work, and sometimes getting the guess wrong.

**Without the skill:**

- **Re-derivation tax, every session.** The same orientation is reconstructed from scratch each time, instead of being read once.
- **Guessed instead of looked up.** Build and test commands, entry points, and conventions get assumed from generic priors rather than this project's reality — and the wrong guess steers the work.
- **Orientation that rots into misinformation.** When a project *does* keep notes, they're trusted blindly long after the code moved — so the map actively misleads instead of helping.
- **No shared home.** Orientation that does get written lands in a scratch file nobody reloads, so it never reaches the next session.

## How the skill helps

It writes the orientation where every session already looks — `AGENTS.md`, the always-on instructions file — and makes keeping it true part of the discipline, because a map injected into every session is misinformation everywhere if it's stale.

**Build (write it down once):**

1. **Survey the codebase.** Purpose, architecture and layout, entry points, confirmed build/test/run commands, observed conventions, gotchas — from the real repo, favouring stable facts and excluding volatile detail (line numbers, WIP) that rots.
2. **Draft the orientation.** From the template, kept tight, with a **freshness marker**: the date and the commit SHA it was validated against.
3. **Write it directly.** Write or refresh the section **in place** in `AGENTS.md` — never an append-only log — and point the user at the file. Because the skill is user-invoked only, the invocation is the authorization and review happens on the diff, not on wording staged in chat (unlike `apw-learn`, which may self-invoke and so proposes-then-confirms).

**Orient (re-enter through it):**

1. **Read the orientation** and its freshness marker, so you know how old it is.
2. **Verify against reality.** `git diff <validated-SHA>..HEAD` shows what changed since; spot-check that named entry points and directories still exist and the commands still work. Where reality disagrees, reality wins.
3. **Refresh on drift** — write the correction straight into `AGENTS.md` for review — then proceed with a verified understanding, relying on the map only where verification confirmed it.

> **The core idea:** the most reused context in any project is "how this codebase is laid out," yet the agent rebuilds it from zero every session. Write it once into the file every session already reads — but because that means it's loaded everywhere, treat staleness as the central risk, not a footnote: stamp it with the commit it was validated against, keep it to slow-changing facts, and verify before trusting. A two-mode discipline that's user-invoked only, so the map is written and trusted on purpose, never by an agent guessing it should.

## Boundary with apw-learn

Both skills write to `AGENTS.md`, so they split it cleanly: `apw-learn` records **prescriptive rules** ("do X, never Y" — corrections, preferences, gotchas-as-rules); `apw-onboard` maintains one **descriptive orientation** section (how the codebase is laid out, built, and run). Each stays in its own section; the orientation never accumulates rules, and the rules never accumulate architecture notes.

## Sample walkthrough

A new project, then a session two weeks later after the code has moved.

### 1 · Building the orientation

```
user: /apw-onboard
assistant: Build mode. Surveyed the repo and confirmed the commands against
  package.json and the CI config. Wrote "## Codebase Orientation" to AGENTS.md:

    Validated: 2026-06-13 against commit a1b2c3d.
    What this is: TypeScript CLI that lints agent-skill manifests.
    Entry points: src/cli.ts (arg parsing) → src/run.ts (orchestration).
    Build: npm run build · Test: npm test · Run: node dist/cli.js.
    Conventions: one command per file under src/commands/; errors via Result<T>.

  AGENTS.md doesn't exist yet — created it with this section.
  Review it in the file: AGENTS.md (Codebase Orientation, validated a1b2c3d).
```

### 2 · Orienting two weeks later

```
user: /apw-onboard "orient"
assistant: Orient mode. Read the orientation (validated a1b2c3d). Checking drift
  against HEAD before trusting it:

  - git diff a1b2c3d..HEAD: src/run.ts was renamed to src/orchestrator.ts.
  - Build/test commands unchanged ✓; src/commands/ convention still holds ✓.

  So the entry-point line was wrong — refreshed it in place to
  src/cli.ts → src/orchestrator.ts and stamped validated: 2026-06-27 @ e4f5g6h.
  Check the diff on AGENTS.md. Continuing from a verified map.
```

The stamped SHA turned "is this still true?" into a one-command diff — so the one stale line was caught and fixed instead of silently misleading every session that loaded `AGENTS.md`.

---

Part of [AgenticPW](../../README.md) · Complements the [apw-learn rationale](apw-learn.md) (both write to `AGENTS.md` — learn owns prescriptive *rules*, onboard owns the descriptive *orientation*) and the [apw-handoff rationale](apw-handoff.md) (handoff persists one task's in-flight *state*; onboard persists the project-wide *map*).
