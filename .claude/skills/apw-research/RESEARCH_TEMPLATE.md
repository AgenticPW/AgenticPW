# Research Template

Copy this structure into your work item's **`research.md`** artifact — see `apw-work-with-work-item-artifacts` for where work-item artifacts live. Every claim must
carry a reference a reader can follow to verify it independently — a file path
with line number, a full URL, a commit SHA, or a command and its output.

```markdown
# Research: <topic>

- **Date:** <YYYY-MM-DD>
- **Status:** draft | complete
- **Slug:** <task-short-slug>

## Question

The core question this research answers, in one sentence.

### Sub-questions
- ...
- ...

### Done-criteria
What "enough to decide" looks like.

### Scope
- **In:** ...
- **Out:** ...

## Findings

For each finding: the claim, where it came from, and how sure we are.

| # | Claim | Reference | Confidence |
|---|-------|-----------|------------|
| 1 | ... | `path/to/file.ts:42` | verified |
| 2 | ... | https://example.com/spec#section | likely |
| 3 | ... | commit `a1b9f3c` / `cmd --flag` output | unconfirmed |

> Confidence: **verified** (checked against a primary source or tested),
> **likely** (one credible source, not cross-checked), **unconfirmed** (single
> weak source or assumption).

## Options & tradeoffs

Compare candidates against the done-criteria. Be honest about the downsides.

| Option | Pros | Cons | Meets criteria? |
|--------|------|------|-----------------|
| A | ... | ... | ... |
| B | ... | ... | ... |

## Recommendation

The concrete recommendation and the reasoning behind it. Name the confidence
level. If this feeds an implementation, say what to build first.

## Open questions & risks

- What could not be determined, and why.
- Assumptions that still need confirming.
- Risks the implementation must carry forward.

## References

Full list of every source consulted, so the report is self-contained:

- [Title or description](url-or-path)
- ...
```
