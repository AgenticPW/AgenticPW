[← All rationales](index.md)

# apw-find-root-and-fix

`/apw-find-root-and-fix "<defect description, or an existing work-item slug>"`

A **command**, not a skill. It carries a known defect to a fix while **scaling the machinery to the problem**. Instead of spawning a fixed pipeline of sub-agents, the command has you do a light familiarization pass yourself, propose the likely cause and a fix, and then let the user choose how much help the defect actually warrants: prove the cause with a diagnosis sub-agent, hand the fix to an implementer sub-agent, or just fix it yourself. Every step happens only after the user approves it.

## The bad practice it prevents

Bug-fixing fails in two opposite directions, and most tooling only guards one of them.

**Over-process — ceremony for a one-line bug.** A rigid "always diagnose, then always spawn an implementer, then always review" pipeline spends three cold-start sub-agent contexts on a defect you could see and fix in a minute. The structure that protects a hard bug becomes pure overhead on an easy one, so people stop using it.

**Under-thinking — a fix with no diagnosis behind it.** The opposite reflex: the first explanation that fits the symptom feels like the answer, so a fix gets aimed at it — usually one level above the real defect. The symptom goes quiet and comes back, or a workaround papers over a cause nobody established.

**Without the command, you also lose:**

- **A pause to confirm the diagnosis.** Diagnosis and fix happen in one breath, with no checkpoint for you to judge whether the cause is actually proven before effort is spent on it.
- **Control over the depth.** The process decides how much rigor to apply; you don't. So it's either too much or too little, never matched to the bug in front of you.

## How the command helps

It makes **proportionality** the design principle and **you** the one who sets the depth. The lead (the command's own context) does a cheap familiarization pass and proposes what's wrong and how to fix it — then offers a fork sized to the defect:

| Path | What runs | When it fits |
|---|---|---|
| **Diagnose deeper** | spawn `apw-root-cause-finder` ([`apw-find-root-cause`](apw-find-root-cause.md)) | cause uncertain, fix risky, competing hypotheses to prove |
| **Hand to the implementer** | spawn `apw-implementer` ([`apw-implement`](apw-implement.md)) | cause clear, but the fix is non-trivial or worth a fresh context |
| **Fix it yourself** | the lead implements directly, no sub-agent | the defect is simple and the fix obvious |
| **Review** (opt-in, after the fix) | spawn `apw-reviewer` ([`apw-review`](apw-review.md)) | you want an adversarial pass on the fix |

1. **You read first, then propose.** The lead familiarizes with the code itself and presents the likely cause, a possible fix, and what it's unsure about — a real opinion to react to, not a blank prompt.
2. **The user sets the depth at a gate.** Knowing the lead's read, you pick the path. A simple bug skips straight to a direct fix; a murky one gets a full diagnosis sub-agent before any code changes. Nothing is spawned and nothing is changed until you choose.
3. **Work already done is passed forward.** When a diagnosis sub-agent *is* spawned, it's handed the lead's familiarization notes — the files read, the hypothesis formed, what couldn't be confirmed — so it builds on that instead of re-searching ground already covered.
4. **Review stays your call.** Even after the fix, the review only runs if you ask for it, and every finding comes back to you to accept, skip, or send for diagnosis first.
5. **Every transition is gated.** Choosing the path, running a review, opening another round — each waits for your explicit go-ahead. Questions a sub-agent hits are surfaced to you, never guessed.

> **The core idea:** the right amount of process for a bug depends on the bug, and only someone who has looked at it can say. This command looks first, proposes a cause and a fix, and then lets you dial the machinery up or down — full diagnosis, a hand-off, or a direct fix — so a trivial defect doesn't pay for a pipeline it doesn't need, and a subtle one still gets its cause proven before anyone touches it.

## When to use it — and when not

Use it when something is **observably broken** and you want the diagnosis-to-fix handled in one flow, with the depth matched to the defect and you in control at each gate. If the root cause is **already proven** (a `root-cause.md` exists), the diagnosis fork adds nothing — use [`apw-implement-review`](apw-implement-review.md). If the fix has a **concrete checkable target** (a reproduction to confirm fixed, a spec), prefer [`apw-implement-verify-review`](apw-implement-verify-review.md) so it's verified, not just reviewed. For open-ended exploration of an unknown problem space, use [`apw-research`](apw-research.md) first — this command diagnoses a *known, observed* defect, not an uncharted one.

## Sample walkthrough — the simple case

The whole reason the command exists: a small bug handled without a pipeline.

```
user: /apw-find-root-and-fix "the 'export CSV' button is greyed out even when there are rows"
```

```
assistant (lead):
  Work item: export-csv-disabled

  I read the export button's enable logic and the rows store.

  What I think is wrong: the button's `disabled` prop is bound to
  `rows.length === 0`, but `rows` is the *filtered* list, which is null
  (not []) before the first filter runs — so `null.length` throws and the
  prop falls back to disabled. (confidence: high)

  Possible fix: guard against the null case — bind to `(rows?.length ?? 0) === 0`,
  and initialize the filtered list to [] instead of null.

  Unsure about: nothing material — the null-vs-[] init is visible in the store.

  How do you want to proceed?
    • Diagnose deeper (spawn root-cause-finder)
    • Hand to the implementer
    • Fix it myself
    • Stop here
```

```
user: fix it yourself
```

```
assistant:
  Done. Initialized the filtered list to [] in store.ts and guarded the
  prop with `(rows?.length ?? 0) === 0` in ExportButton.tsx.

  Want a review of the fix, or stop here?
user: stop here.

  Summary: the export button was disabled because the filtered-rows list was
  null before the first filter, throwing on `.length`. Fixed by initializing
  it to [] and null-guarding the prop. No sub-agents needed; no review run.
```

No diagnosis sub-agent, no implementer sub-agent, no forced review — three cold-start contexts saved on a bug that didn't need them. For a defect whose cause was murky, the same opening read would have ended with the user choosing **Diagnose deeper**, and the lead's notes would have been handed straight to the root-cause-finder so it didn't start cold.

---

Part of [AgenticPW](../../README.md). The command is Claude-Code-specific (a slash command plus subagents); the skills it orchestrates work in any tool that follows the Agent Skills standard.
