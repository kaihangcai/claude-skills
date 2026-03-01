# Claude Code Skills

A collection of global skills for [Claude Code](https://claude.ai/claude-code).

## Skills

| Skill | Trigger | Description |
|-------|---------|-------------|
| `dev-docs` | After accepting a plan | Creates task documentation — `plan.md`, `decisions.md`, and `checklist.md` — under `.claude/dev-docs/active/<task-id>/` |
| `update-dev-docs` | After completing a chunk of work | Syncs checklist progress, records new decisions, bumps timestamps, and proposes status transitions |

## Installation

Skills are installed globally so they're available in every project.

**Copy each skill directory into `~/.claude/skills/`:**

```bash
cp -r skills/dev-docs     ~/.claude/skills/dev-docs
cp -r skills/update-dev-docs ~/.claude/skills/update-dev-docs
```

On Windows (Git Bash):

```bash
cp -r skills/dev-docs     "$USERPROFILE/.claude/skills/dev-docs"
cp -r skills/update-dev-docs "$USERPROFILE/.claude/skills/update-dev-docs"
```

Restart Claude Code after copying. Each skill will then appear in the skill list and can be invoked via its name or the Skill tool.

## Adding new skills

1. Create a subdirectory under `skills/` named after the skill (e.g. `skills/my-skill/`)
2. Add a `SKILL.md` file inside it with a frontmatter block and the skill instructions:

```markdown
---
name: my-skill
description: One-sentence description used by Claude to decide when to invoke this skill.
allowed-tools: Read(**), Write(**), Bash(...)
---

# my-skill — Title

Instructions for Claude...
```

3. Copy the new directory to `~/.claude/skills/my-skill/` and restart Claude Code.
