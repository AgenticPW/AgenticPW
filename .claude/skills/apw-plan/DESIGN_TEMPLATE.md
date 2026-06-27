# Design Template

Copy this structure into your work item's **`design.md`** artifact — see `apw-work-with-work-item-artifacts` for where work-item artifacts live. Give every design
decision a stable ID (D1, D2, …) so the plan can reference it, and tie each one
back to the requirement(s) it serves. Design covers **both** the technical
design (the main tech decisions) and — *only when the task has a user-facing
surface* — the **UI design** as ASCII mockups. Delete the UI section when there
is no UI.

````markdown
# Design: <task>

- **Date:** <YYYY-MM-DD>
- **Slug:** <task-short-slug>
- **Status:** draft | confirmed
- **Implements:** <link to requirements.md, if any>

## Overview

The shape of the solution in a few sentences — the approach at a high level
before the details.

## Technical design

The main technical decisions and how the pieces fit. For each significant
decision, give it an ID, state the choice, the rationale, and the
requirement(s) it serves.

| ID | Decision | Rationale | Serves |
|----|----------|-----------|--------|
| D1 | ... | ... | R1, R2 |
| D2 | ... | ... | R3 |

Expand on anything that needs more than a table row — data model, control flow,
key interfaces, error handling, the components touched and how they interact.

## UI design

*(Only if the task has a user-facing surface — otherwise delete this section.)*

Sketch the interface as ASCII mockups so the layout is concrete before it's
built. One mockup per significant screen or state.

```
+----------------------------------------+
|  Title                          [x]    |
+----------------------------------------+
|  ...                                   |
|                                        |
|                          [ Cancel ][OK]|
+----------------------------------------+
```

Note the key interactions, the states (empty / loading / error), and anything
the mockup can't show.

## Alternatives considered

Approaches weighed and rejected, with the reason — so the choice isn't
relitigated later.

- ...

## Risks

- ...

## Open questions

- ...
````
