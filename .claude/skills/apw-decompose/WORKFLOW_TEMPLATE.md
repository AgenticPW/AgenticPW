# Workflow Template

Copy this structure into your work item's **`workflow.md`** artifact — see
`apw-work-with-work-item-artifacts` for where work-item artifacts live.

````markdown
# Workflow: <task>

- **Date:** <YYYY-MM-DD>
- **Slug:** <task-short-slug>
- **Status:** draft | confirmed

## Task

One or two sentences describing what needs to be done, as understood from the brief.

## Assessment

What the agent knows and does not know going in:

- **Requirements:** <clear / fuzzy — one sentence>
- **Technical unknowns:** <none / specific unknowns named>
- **Design space:** <no meaningful choices / choices exist — named briefly>
- **Task size:** <fits in one session / needs sub-task ordering>
- **Type:** feature | bug-fix | refactor | other

## Workflow

### Step 1: `<skill-name>` — <short label for this step>
**Why:** <one or two sentences — why this specific skill is needed for this specific task>
**Produces:** `<artifact filename or outcome>`

### Step 2: `<skill-name>` — <short label>
**Why:** ...
**Produces:** ...

*(Add or remove steps as needed. Order matters — dependencies come first.)*

## Conditions and risks

Anything that could change the workflow mid-execution: a research finding that
might collapse the design space, a dependency that could block implementation,
an assumption that, if wrong, would require backtracking.

- ...
````
