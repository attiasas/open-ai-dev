# Working on oss-cycle

This repository is the `oss-cycle` plugin. GitHub issues are the backlog. The design record is [docs/superpowers/specs/2026-11-11-oss-cycle-design.md](docs/superpowers/specs/2026-11-11-oss-cycle-design.md). The catalog is [SKILLS.md](SKILLS.md).

## Adding a skill

1. Pick one issue that is `status:ready`.
2. Move it to `status:in-progress`.
3. Add `skills/<name>/SKILL.md` on a branch named `issue-<number>-<slug>`.
4. User-invoked skills (`setup-cycle`, `plan`, `manage`, `architect`, `develop`, `qa`) set `disable-model-invocation: true`.
5. Model-invoked skills omit that flag.
6. The description is third person and says what the skill does and when to use it.
7. Keep `SKILL.md` under 500 lines. Put longer reference material in a sibling file one level down.
8. In the same change, set that skill's catalog status in `SKILLS.md` to `written`.
9. Open a pull request that links the issue.

Do not add an empty skill. A `SKILL.md` with no procedure shows up as a command.

## Record

Specs and task breakdowns for this plugin live on GitHub issues. An architecture decision, if one is needed while building the plugin, is `docs/adr/NNNN-title.md` plus a `type:decision` issue that links to that file.
