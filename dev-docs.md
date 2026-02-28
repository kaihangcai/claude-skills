---
description: Create a local dev-docs directory for any planned task — writes plan.md, decisions.md, and checklist.md under .claude/dev-docs/active/<task-id>/. No TASKS.md or Jira integration; purely a local tracker.
allowed-tools: Read(CLAUDE.local.md), Write(.claude/dev-docs/**), Bash(ls)
argument-hint: [<task-id>] — slug (e.g. 2026-02-28-cache-refactor); omit to derive from plan title
# examples:
#   /dev-docs-general                           # derive task-id from current conversation context
#   /dev-docs-general 2026-02-28-cache-refactor # explicit slug
---

# dev-docs-general — Initialise Local Task Documentation

Create the three dev-doc files for the most recently accepted plan.
Run this **immediately** after the user accepts a plan and exits plan mode.

This skill is a standalone local tracker — it has **no TASKS.md or Jira integration**.
Use `/dev-docs` instead when the task is tracked in TASKS.md.

---

## Status System

Dev-doc tasks have exactly three statuses.

| Status | Directory | Meaning |
|--------|-----------|---------|
| Active | `.claude/dev-docs/active/<task-id>/` | Default for all new plans; work is ongoing |
| Blocked | `.claude/dev-docs/blocked/<task-id>/` | Work is paused due to an external dependency |
| Completed | `.claude/dev-docs/completed/<task-id>/` | All checklist items done; task closed |

**Status changes require explicit user permission.** Never move a task between directories
without asking first. See `/update-dev-docs-general` for how to request a status change.

---

## Steps

### 1. Determine task-id

- If an argument was passed, use it exactly.
- Otherwise derive a short slug from the plan title: `<YYYY-MM-DD>-<kebab-case-summary>`.
- If still ambiguous, ask the user before proceeding.

Task IDs are always slugs — there is no Jira key lookup.

### 2. Resolve the dev-docs directory

All new plans default to **Active**:

```
.claude/dev-docs/active/<task-id>/
```

**Existing task — choose the right action first:**

Before writing anything, check whether a dev-docs directory already exists for this task-id
(search all three status folders: `active/`, `blocked/`, `completed/`).

If it does, determine the nature of the change:

| Scenario | Correct action |
|----------|---------------|
| **Plan refinement** — same task, same goal, approach adjusted/improved | Stop. Tell the user to run `/update-dev-docs-general <task-id>` instead; record the updated plan text under `## Deviations from Plan` in `decisions.md` and update `plan.md` there. Do **not** reset checkboxes. |
| **Full re-plan** — scope changed significantly, checkboxes should start fresh | Proceed below: overwrite files and reset checklist. |

For a **full re-plan** on an existing directory:
- `plan.md` — overwrite with the new plan
- `decisions.md` — preserve existing content, add a `## Revision <N>` section at the top
- `checklist.md` — reset all checkboxes to unchecked, update `Last Updated`, reset status to `Active`

Do **not** move the task to a different status folder during a re-plan.

### 3. Write `plan.md`

```markdown
# Plan — <task-id>

> Generated: <YYYY-MM-DD HH:MM>

## Objective
<one-sentence goal extracted from the plan>

## Approach
<summary of the chosen strategy>

## Accepted Plan

<full plan text verbatim, exactly as accepted>
```

### 4. Write `decisions.md`

```markdown
# Key Files & Decisions — <task-id>

> Last Updated: <YYYY-MM-DD HH:MM>

## Key Files

List every source file identified in the plan as likely to be created or modified.

| File | Change |
|------|--------|
| `path/to/File.java` | Brief description |

## Architectural Decisions

| Decision | Rationale | Trade-offs |
|----------|-----------|------------|
| e.g. Use copy constructor for config | Avoid mutating caller's config | Slightly more memory |

## Deviations from Plan
*(Updated during execution — record any mid-task pivots here)*
```

### 5. Write `checklist.md`

Extract every distinct action item from the accepted plan as checkboxes, grouped under the
same headings used in the plan.

```markdown
# Task Checklist — <task-id>

> Last Updated: <YYYY-MM-DD HH:MM>
> Status: Active

## <Phase / Section from plan>

- [ ] <action item>
- [ ] <action item>

## <Next phase>

- [ ] <action item>
```

### 6. Confirm

```
✅ Dev docs created at .claude/dev-docs/active/<task-id>/
   plan.md · decisions.md · checklist.md
```
