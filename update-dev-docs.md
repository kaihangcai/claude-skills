---
description: Update the local dev-docs for any tracked task — syncs checklist progress, bumps Last Updated timestamp, records new decisions or key file discoveries, and proposes status transitions with user permission. No TASKS.md or Jira integration.
allowed-tools: Read(.claude/dev-docs/**), Write(.claude/dev-docs/**), Edit(.claude/dev-docs/**/checklist.md, .claude/dev-docs/**/decisions.md), Bash(mv)
argument-hint: [<task-id>] — omit to infer from conversation context
# examples:
#   /update-dev-docs-general                          # infer active task from conversation context
#   /update-dev-docs-general 2026-02-28-cache-refactor # explicit task slug
---

# update-dev-docs-general — Sync Local Task Documentation

Update checklist progress and record decisions for a dev-docs task.
Invoke this after completing any meaningful chunk of work.

This skill is a standalone local tracker — it has **no TASKS.md or Jira integration**.
Use `/update-dev-docs` instead when the task is tracked in TASKS.md.

---

## Status System (read-only reference)

| Status | Directory |
|--------|-----------|
| Active | `.claude/dev-docs/active/<task-id>/` |
| Blocked | `.claude/dev-docs/blocked/<task-id>/` |
| Completed | `.claude/dev-docs/completed/<task-id>/` |

**Status changes require explicit user permission.** This skill may *propose* a change
but must receive a clear "yes" / "confirm" / "go ahead" before moving any files.

---

## Steps

### 1. Determine task-id

- If an argument was passed, use it.
- Otherwise infer from the conversation — look for a task slug recently mentioned or
  the most recently active plan.
- If multiple candidates exist, ask the user which task to update.

### 2. Locate the dev-docs directory

Search all three status folders in order: `active/`, `blocked/`, `completed/`.

```
.claude/dev-docs/active/<task-id>/
.claude/dev-docs/blocked/<task-id>/
.claude/dev-docs/completed/<task-id>/
```

Note the current status from whichever folder the task is found in.
If not found in any folder, stop and suggest running `/dev-docs-general` first.

### 3. Update `checklist.md`

Read `checklist.md`. For each item:
- If the work described has been completed in this session, mark it `[x]`.
- If partially done, leave as `[ ]` with an inline note: `[ ] <item> *(partial: ...)*`.
- Never uncheck a previously checked item unless explicitly asked by the user.

Bump `Last Updated` to the current date and time.

The `Status` line reflects **only** the three allowed values — do not update it here;
status changes are handled in Step 5.

```markdown
> Last Updated: <YYYY-MM-DD HH:MM>
> Status: <current status — unchanged unless Step 5 is executed>
```

### 4. Update `decisions.md`

Record any of the following that occurred since the last update:

- **New key files:** rows in `## Key Files` for files created, read, or significantly
  modified but not in the original plan.
- **New decisions:** rows in `## Architectural Decisions` for choices that differ from
  or extend the plan.
- **Plan deviations:** entries under `## Deviations from Plan` explaining any pivot.

If nothing new to record, leave `decisions.md` untouched.

### 5. Assess whether a status change is warranted

After updating the checklist, evaluate:

| Condition | Proposed transition |
|-----------|-------------------|
| All checklist items are `[x]` | Active / Blocked → **Completed** |
| A clear blocker was mentioned in the conversation | Active → **Blocked** |
| The blocker described in a Blocked task has been resolved | Blocked → **Active** |

If any condition is met, **ask the user** before making any change:

```
⚠️ Status change proposed
   Task:     <task-id>
   Current:  <current status>
   Proposed: <new status>
   Reason:   <brief reason>

   Shall I move this task to <new status>? (yes / no)
```

Only proceed to Step 6 if the user gives explicit confirmation.
If the user says no, keep the current status and note it in the confirmation message.

### 6. Execute status transition (only with user confirmation)

1. Move the task directory:
   ```bash
   mv ".claude/dev-docs/<current-status>/<task-id>" ".claude/dev-docs/<new-status>/<task-id>"
   ```
2. Update the `Status` line in `checklist.md` to the new status value.

### 7. Confirm

```
✅ Dev docs updated for <task-id>
   checklist.md — N items checked, Last Updated bumped
   decisions.md — <M new key files / K new decisions / no changes>
   Status — <unchanged | moved: old → new>
```
