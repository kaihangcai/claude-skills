# Claude Code Skills Repo

This repository is a version-controlled store of globally reusable Claude Code skills that live on the local machine at `~/.claude/skills/`.

## Purpose

Skills here are **not project-specific** — they are general-purpose utilities intended to be installed globally and work across any project. The repo exists so skills can be tracked in version control, shared across devices, and restored after a reinstall.

## Structure

```
skills/
  <skill-name>/
    SKILL.md        # Skill definition loaded by Claude Code
  sync-global-skills/
    SKILL.md        # Repo-local skill: syncs ~/.claude/skills/ into this repo
README.md           # Setup and installation instructions
CLAUDE.md           # This file
```

## Key conventions

- Each skill lives in its own subdirectory under `skills/` with a single `SKILL.md`.
- Skill names (the `name:` frontmatter field) match their directory name.
- `sync-global-skills` is the only repo-local skill — it is **not** installed globally. All other skills under `skills/` are installed at `~/.claude/skills/`.
- To install skills on a new device, copy each skill directory into `~/.claude/skills/`. See `README.md` for exact commands.

## Keeping skills up to date

Use the `sync-global-skills` skill (project-local) to pull the latest versions of all globally installed skills back into this repo before committing.
