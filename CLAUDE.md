# Claude Code Skills Repo

This repository is a **Claude Code plugin** that provides globally reusable dev-workflow skills. It is also its own marketplace, so it can be installed on any machine via a single `/plugin` command.

## Purpose

Skills here are **not project-specific** — they are general-purpose utilities that work across any project. The repo exists so skills can be tracked in version control, shared across devices, and restored after a reinstall.

## Structure

```
.claude-plugin/
  plugin.json         # Plugin manifest (name, version, description)
  marketplace.json    # Marketplace catalog — lists this plugin as its own source
skills/
  <skill-name>/
    SKILL.md          # Skill definition loaded by Claude Code
README.md             # Setup and installation instructions
CLAUDE.md             # This file
```

## Key conventions

- Each skill lives in its own subdirectory under `skills/` with a single `SKILL.md`.
- Skill names (the `name:` frontmatter field) match their directory name.
- Skills are namespaced by the plugin: invoked as `/dev-workflow-skills:<skill-name>`.
- All skills are global — installed once and available in every project.

## Adding or updating skills

1. Add/edit the `SKILL.md` under `skills/<skill-name>/`.
2. Bump `version` in `.claude-plugin/plugin.json` (e.g. `1.0.1`).
3. Commit and push.
4. Claude Code auto-updates on the next startup (if auto-update is enabled for this marketplace), or run `/plugin marketplace update dev-workflow-skills` manually.

## Installing on a new machine

```shell
/plugin marketplace add kaihangcai/claude-skills
/plugin install dev-workflow-skills@dev-workflow-skills
```

See `README.md` for full installation options.
