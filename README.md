# Claude Code Skills

A [Claude Code plugin](https://code.claude.com/docs/en/plugins) providing personal dev-workflow skills available across all projects and machines.

## Skills

| Skill | Trigger | Description |
|-------|---------|-------------|
| `dev-workflow-skills:dev-docs` | After accepting a plan | Creates task documentation — `plan.md`, `decisions.md`, and `checklist.md` — under `.claude/dev-docs/active/<task-id>/` |
| `dev-workflow-skills:update-dev-docs` | After completing a chunk of work | Syncs checklist progress, records new decisions, bumps timestamps, and proposes status transitions |
| `dev-workflow-skills:plan-reviewer` | To audit a plan | Cross-checks a dev-docs plan against the real codebase and produces a structured review report |

## Installation

### Prerequisites

Claude Code v1.0.33 or later — run `claude --version` to check.

### Add as a marketplace (recommended for multiple machines)

Push this repo to GitHub (or any git host), then on each machine run:

```shell
/plugin marketplace add your-username/your-repo-name
/plugin install dev-workflow-skills@dev-workflow-skills
```

Enable auto-updates so skills stay current whenever you push changes:

1. Run `/plugin` → **Marketplaces** tab
2. Select `dev-workflow-skills`
3. Choose **Enable auto-update**

### Add as a local plugin (single machine)

Clone the repo locally, then run Claude Code with the plugin directory flag:

```bash
claude --plugin-dir /path/to/this/repo
```

Or add it permanently via the marketplace:

```shell
/plugin marketplace add /path/to/this/repo
/plugin install dev-workflow-skills@dev-workflow-skills
```

## Adding new skills

1. Create a subdirectory under `skills/` named after the skill (e.g. `skills/my-skill/`)
2. Add a `SKILL.md` file with frontmatter and instructions:

```markdown
---
name: my-skill
description: One-sentence description used by Claude to decide when to invoke this skill.
allowed-tools: Read(**), Write(**), Bash(...)
---

# my-skill — Title

Instructions for Claude...
```

3. Bump the `version` in `.claude-plugin/plugin.json`, commit, and push.
4. Claude Code will pick up the new skill on the next auto-update (or restart).
