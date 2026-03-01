---
name: plan-reviewer
description: Second-opinion review of a newly created dev-docs plan. Reads plan.md, decisions.md, and checklist.md for a task, cross-checks against the real codebase, and produces a structured report of confirms, warnings, blockers, and suggestions. Then proposes targeted checklist and decisions.md updates for any issues found, applying them with user permission. Invoke when the user asks to "review", "check", or "audit" a dev-docs task, or passes a task ID like "review T2".
allowed-tools: Read, Glob, Grep, Bash(ls, date), Write(.claude/dev-docs/**), Edit(.claude/dev-docs/**/checklist.md, .claude/dev-docs/**/decisions.md)
---

# plan-reviewer — Dev-Docs Plan Review

Perform a structured second-opinion review of a dev-docs plan.
Your job is to **catch problems before implementation starts**, not to rewrite the plan.

---

## Input

- An argument (task ID like `T2`, or task slug like `2026-03-01-expedition-journal`), OR
- No argument → find the most recently modified active task directory.

---

## Steps

### 1. Resolve the task

1. Read `.claude/dev-docs/planned-tasks.md` to find the task's directory path.
2. Read all three dev-doc files:
   - `.claude/dev-docs/<status>/<task-dir>/plan.md`
   - `.claude/dev-docs/<status>/<task-dir>/decisions.md`
   - `.claude/dev-docs/<status>/<task-dir>/checklist.md`

### 2. Cross-check the plan against the codebase

For every **MODIFY** file in the plan's file manifest:
- Read the file and confirm it exists and matches what the plan expects.

For every **CREATE** file:
- Check it doesn't already exist (would indicate a stale or conflicting plan).

For every import, type, constant, or function reference mentioned in the plan:
- Search the codebase to confirm it exists at the expected path.
- Flag any that are missing, renamed, or at a different path.

Check schema additions against the existing Prisma schema to detect conflicts.

### 3. Get the current date

Before writing the report, run the following Bash command to get the real current date:

```bash
date "+%Y-%m-%d"
```

Use the output as the `> Reviewed:` value. **Never guess or hardcode a date.**

### 4. Produce the review report

Output a structured markdown report with these sections:

```
# Plan Review — <task-id>
> Reviewed: <YYYY-MM-DD>

## Summary
One-paragraph overall assessment: ready to implement / needs minor fixes / has blockers.

## ✅ Confirmed
Bullet list of plan elements that are correct and verified against the codebase.
(imports that exist, files at the right path, patterns that match, etc.)

## ⚠️ Warnings
Bullet list of issues that won't block implementation but should be noted.
Each warning: what the plan says → what was found → recommended fix.

## ❌ Blockers
Bullet list of issues that WILL cause failures if not resolved first.
Each blocker: what the plan says → what was found → required fix.

## 📋 Checklist Audit
- Does the checklist cover every action item in the plan? (yes / missing items listed)
- Are there any checklist items with no corresponding plan section? (yes / listed)
- Are the phases in the correct dependency order?

## 💡 Suggestions
Optional improvements not in the original plan — keep this short (≤5 items).
Do not suggest scope creep. Focus on things that reduce implementation risk.
```

### 5. Write the report

Save the report to:
```
.claude/dev-docs/<status>/<task-dir>/review.md
```

Then print the full report to the conversation so the user can read it immediately.

### 6. Propose dev-docs updates (requires user permission)

After printing the report, assess whether `checklist.md` or `decisions.md` need changes:

**Triggers for a proposed update:**
- Any ❌ blocker whose fix requires a new or corrected checklist item
- Any checklist item identified as missing in the 📋 Checklist Audit
- Any blocker or warning that represents an architectural decision not yet in `decisions.md`

If any trigger is met, present a **single consolidated proposal** before touching any file:

```
⚠️  Dev-docs update proposed for <task-id>

checklist.md — <N> changes:
  + [Phase X] <new item text>
  ~ [Phase Y] "<old item text>" → "<corrected item text>"

decisions.md — <M> changes:
  + New decision: "<decision title>"
  + New deviation: "<deviation summary>"

Shall I apply these updates? (yes / no)
```

**If the user confirms (yes / go ahead / confirm):**
1. Apply all checklist changes (add missing items with review annotations, correct
   mislabelled items).
2. Add new rows to `## Architectural Decisions` in `decisions.md` for each blocker
   decision.
3. Append a dated subsection under `## Deviations from Plan` in `decisions.md` for
   each blocker that contradicts the original plan text.
4. Bump `Last Updated` in both files.
5. Confirm:
   ```
   ✅ Dev docs updated — checklist.md (<N> items), decisions.md (<M> entries)
   ```

**If the user declines (no / skip / later):**
- Do not modify any files.
- Confirm:
  ```
  ℹ️  No dev-docs changes applied. Review saved to review.md only.
  ```

**If no updates are needed** (no blockers, no missing items, no new decisions):
- Skip the proposal entirely and proceed directly to Step 7.

### 7. Final confirmation

```
✅ Review complete. Report saved to .claude/dev-docs/<status>/<task-dir>/review.md
```
