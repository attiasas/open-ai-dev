# Skills

Catalog for `oss-cycle`. Status stays `planned` until `skills/<name>/SKILL.md` exists. GitHub issue labels are the work state, separate from this status.

Phase skills are user-invoked and do not call each other. They call the model-invoked skills in the Calls column.

## User-invoked

| Skill | Calls | Writes | Status |
| --- | --- | --- | --- |
| `setup-cycle` | `github-record` | Labels, issue forms, pull request template, `AGENTS.md`, `docs/adr/0000-template.md` | written |
| `plan` | `spec-issue`, then `slice-work` only when there is more than one mergeable piece | Spec and slice issues | planned |
| `manage` | `github-record` | Label changes and short comments. Moves a blocked slice to `status:ready` when its blockers are closed | planned |
| `architect` | `module-design` | An ADR file and the pull request that adds it | planned |
| `develop` | `tdd` | Code and tests on `issue-<number>-<slug>`, and a pull request for one ready slice or bug | planned |
| `qa` | `review-pr` | A pass or fail comment on the pull request | planned |

## Model-invoked

| Skill | Called by | Writes | Status |
| --- | --- | --- | --- |
| `github-record` | `setup-cycle`, `manage`, and any skill that touches GitHub | The label set, `gh` usage, and where text lives | written |
| `spec-issue` | `plan` | Issue body: Problem, Acceptance, Non-goals, Modules, ADRs | written |
| `slice-work` | `plan` | Child issues with GitHub blocked-by links | written |
| `module-design` | `architect` | `docs/adr/NNNN-title.md` and the pull request that adds it | written |
| `tdd` | `develop` | A red-green-refactor loop for one issue, using the project's test command | planned |
| `review-pr` | `qa` | Verdict comment: acceptance criteria, ADRs, test command result | planned |

## Build order

GitHub labels are the work state. The table is the blocker edges. A closed blocker does not change a label by itself.

| Issue | Skill | Blocked by |
| --- | --- | --- |
| [#1](https://github.com/attiasas/open-ai-dev/issues/1) | `github-record` | — |
| [#2](https://github.com/attiasas/open-ai-dev/issues/2) | `setup-cycle` | #1 |
| [#3](https://github.com/attiasas/open-ai-dev/issues/3) | `spec-issue` | #1 |
| [#4](https://github.com/attiasas/open-ai-dev/issues/4) | `slice-work` | #1 |
| [#5](https://github.com/attiasas/open-ai-dev/issues/5) | `module-design` | #1 |
| [#6](https://github.com/attiasas/open-ai-dev/issues/6) | `tdd` | #1 |
| [#7](https://github.com/attiasas/open-ai-dev/issues/7) | `review-pr` | #1 |
| [#8](https://github.com/attiasas/open-ai-dev/issues/8) | `manage` | #1 |
| [#9](https://github.com/attiasas/open-ai-dev/issues/9) | `plan` | #3, #4 |
| [#10](https://github.com/attiasas/open-ai-dev/issues/10) | `architect` | #5 |
| [#11](https://github.com/attiasas/open-ai-dev/issues/11) | `develop` | #6 |
| [#12](https://github.com/attiasas/open-ai-dev/issues/12) | `qa` | #7 |
