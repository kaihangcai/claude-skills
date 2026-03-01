---
name: sync-global-skills
description: Sync global Claude Code skills into this repository. Copies every SKILL.md from ~/.claude/skills/ into the repo's skills/ directory, adding new skills and updating changed ones. Invoke when the user says "sync skills", "update the skills repo", or "pull global skills into the repo".
allowed-tools: Bash(ls, find, cp, mkdir, diff)
---

# sync-global-skills — Pull Global Skills into Repo

Copy every installed global skill into the repo's `skills/` directory, keeping the repository up to date with what is actually installed in `~/.claude/skills/`.

---

## Steps

### 1. Resolve the global skills path

Run the following to confirm the path exists:

```bash
ls ~/.claude/skills/
```

Each subdirectory listed is an installed skill. Collect the full list.

### 2. For each skill, sync SKILL.md into the repo

For every skill directory found in `~/.claude/skills/`:

```bash
mkdir -p skills/<skill-name>
cp ~/.claude/skills/<skill-name>/SKILL.md skills/<skill-name>/SKILL.md
```

Track whether each skill was **new** (repo directory did not previously exist) or **updated** (directory already existed — use `diff` first to confirm the file actually changed).

### 3. Confirm

```
✅ Skills synced from ~/.claude/skills/
   New:     <comma-separated list of newly added skills, or "none">
   Updated: <comma-separated list of changed skills, or "none">
   Total:   N skills in skills/
```
