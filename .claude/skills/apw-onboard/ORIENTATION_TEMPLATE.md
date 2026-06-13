# Orientation Template

Copy this structure into a **Codebase Orientation** section of `AGENTS.md` — the
always-on instructions file every session reads. This section is injected into
*every* session, so keep it tight: record only stable, slow-changing facts and
prune anything a session can cheaply rediscover. A wrong orientation loaded into
every session is worse than none — keep the freshness marker honest and refresh
on drift. Confirm against the real repo (read the config, run the build/test
commands) rather than trusting memory or a possibly-stale README.

````markdown
## Codebase Orientation

<!-- Maintained by apw-onboard. Descriptive map only — prescriptive rules
     ("do X, never Y") live in their own section, maintained by apw-learn. -->

_Validated: <YYYY-MM-DD> against commit <short-SHA>._

### What this is

One or two sentences: the project's purpose, and the languages and frameworks
it's built on.

### Architecture & layout

The major components and how they fit together; the top-level directories and
what each holds.

- `path/` — what lives here and why
- ...

### Entry points

Where execution starts, and the few files to read first.

- `path/to/entry` — ...
- ...

### Build · test · run

The exact, confirmed commands.

- **Build:** `...`
- **Test:** `...`
- **Lint:** `...`
- **Run locally:** `...`

### Conventions

Patterns this codebase actually follows (naming, layering, error handling,
module organization), as observed in the code.

- ...

### Gotchas

Non-obvious constraints, required setup, or traps that cost a session time.

- ...
````
