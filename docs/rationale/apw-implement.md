[← All rationales](index.md)

# apw-implement

`/apw-implement "<feature or fix>"`

A structured implementation workflow: familiarize → ask → plan → confirm → implement → summarize. It trades a few minutes of up-front discipline for far fewer wrong-direction rewrites.

## The bad practice it prevents

When asked to "just implement this," the natural instinct is to open the most obvious file and start typing. It feels fast. It usually isn't.

**Without the skill:**

- **Coding on assumptions.** You guess at how the existing system works instead of reading it, and build on a wrong mental model.
- **Silent ambiguity.** A vague request gets one of several possible interpretations — and you find out it was the wrong one at review time.
- **No agreed plan.** The first sign the approach is wrong is a large diff that has to be thrown away.
- **Hidden workarounds.** A mid-implementation surprise gets papered over with a hack nobody agreed to, buried in the change.
- **Rosy summaries.** The work is reported as "done" while real gaps and tradeoffs go unmentioned.

## How the skill helps

It replaces the impulse to dive in with six ordered steps. Each one is a checkpoint that catches a class of mistake before it becomes expensive.

1. **Familiarize.** Read the relevant code, docs, and prior work *before* forming any opinion — so the plan is grounded in reality, not assumptions.
2. **Ask (up to 5, only if needed).** Surface genuine ambiguity as targeted questions. Skip entirely when the task is already clear — no busywork.
3. **Plan & confirm.** State the approach, the files affected, and any tradeoffs, then wait for **"Ready to proceed?"** Big tasks are flagged for splitting instead of half-built.
4. **Implement.** Execute the agreed plan, tracking each step so nothing is silently dropped.
5. **Surface conflicts.** If reality contradicts the plan, stop and raise it — no unilateral workarounds.
6. **Honest summary.** Report what was done, what was worked around and why, and what still needs follow-up.

> **The core idea:** the most expensive bug is the feature you built correctly — but wasn't what anyone wanted. This skill front-loads the cheap checks (reading, asking, agreeing) so the expensive work only happens once.

## Sample walkthrough

A developer asks for what looks like a one-line change. Watch how the skill keeps it from going sideways.

### 1 · The request

```
user: /apw-implement "make the export button also email the report"
```

### 2 · Familiarize, then ask only what matters

```
assistant: I read the export handler and the existing mailer.
The export is generated client-side; there's no server route that
has access to the user's email. Two genuine unknowns:

  1. Email which report — the current filtered view, or the full dataset?
  2. Send to the logged-in user, or let them enter a recipient?
```

### 3 · Plan and confirm — no code yet

```
assistant: Plan:
  • Add POST /api/reports/email that renders server-side and sends
    via the existing mailer.
  • Wire the button to call it after export, recipient = current user.
  Files: routes/reports.ts, services/mailer.ts, ui/ExportButton.tsx
  Tradeoff: server-side render duplicates ~20 lines of the client
  formatter — I'll extract a shared helper.

  Ready to proceed?
user: yes
```

### 4 · Implement, surface a conflict, finish honestly

```
assistant: While wiring the mailer I found it only accepts
plaintext, not the HTML report. That contradicts the plan — stopping
to flag it rather than silently attaching a worse plaintext version.

user: use the HTML-capable transport in services/mailer2.ts
assistant: Done.

Summary
  • Added /api/reports/email + button wiring; uses mailer2 (HTML).
  • Extracted shared formatter to formatReport().
  Gap: no rate-limiting on the endpoint yet — recommend follow-up.
```

The "one-line change" touched four files, hit a real constraint, and shipped with a known follow-up named out loud — none of which would have surfaced from typing into the first file that looked relevant.

---

Part of [AgenticPW](../../README.md) · See also the [apw-commit rationale](apw-commit.md).
