# oss-cycle

A Cursor plugin for the basic engineering cycle on an open source repository: plan, manage, architect, develop, and QA.

GitHub issues, pull requests, and comments are the work record. Architecture decisions are files in `docs/adr/`, and a GitHub issue links to each file. Cursor with Grok is the default agent. The skills are plain `SKILL.md` files, so Claude, Codex, or another agent can load the same tree.

The plugin id is `oss-cycle`. This repository is [attiasas/open-ai-dev](https://github.com/attiasas/open-ai-dev).

## Status

The skill catalog is in [SKILLS.md](SKILLS.md). Every skill is `planned`. No `SKILL.md` files are shipped yet, so installing the plugin today adds no commands. Each skill is added when its GitHub issue is implemented.

The design is in [docs/superpowers/specs/2026-11-11-oss-cycle-design.md](docs/superpowers/specs/2026-11-11-oss-cycle-design.md).

## Install

### Cursor

Clone this repository and symlink it where Cursor loads local plugins:

```bash
git clone https://github.com/attiasas/open-ai-dev.git
mkdir -p ~/.cursor/plugins/local
ln -s "$(pwd)/open-ai-dev" ~/.cursor/plugins/local/oss-cycle
```

Reload Cursor. Use Grok. Confirm `oss-cycle` under Customize.

### Claude, Codex, and other agents

When skills exist, install the same `skills/` tree with the skills CLI:

```bash
npx skills add attiasas/open-ai-dev
```

Until then, the catalog in `SKILLS.md` is the list of what will be installed.

## Cycle

| Command | What it leaves behind |
| --- | --- |
| `setup-cycle` | Labels, issue forms, a pull request template, `AGENTS.md`, and `docs/adr/0000-template.md` |
| `plan` | A spec issue, sliced into blocked child issues when the work is more than one merge |
| `manage` | Label changes and short comments on the backlog |
| `architect` | An ADR in `docs/adr/` and a decision issue that links to it |
| `develop` | Code, tests, and a pull request for one ready issue |
| `qa` | A pass or fail comment on the pull request |

A human merges. QA does not merge.
