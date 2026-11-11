# OSS Cycle

A Cursor plugin other maintainers install into an open source repository. It runs a basic engineering cycle: plan, manage, architect, develop, and QA.

Cursor with Grok is the default agent. Skills are plain `SKILL.md` files, so Claude, Codex, or another agent can load the same tree.

The GitHub repository for the plugin is `attiasas/open-ai-dev`. The plugin id is `oss-cycle`.

## Record

GitHub issues, pull requests, and comments are the work record.

Architecture decisions are files in the target repository at `docs/adr/NNNN-title.md`. The pull request that adds the file is the GitHub record. There is no decision issue and no `type:decision` label. A candidate with no file yet does not get an issue. See [ADR 0001](../../adr/0001-adr-file-and-pull-request.md).

Specs, task breakdowns, and QA notes stay on GitHub. The target repository keeps code, a short `AGENTS.md`, issue and pull request templates, and `docs/adr/`.

Setup does not ask which tracker to use.

## Cycle

Five thin commands are the public interface, plus a one-time setup. Smaller skills hold the reusable discipline. A phase skill does not call another phase skill.

The cycle stops when a pull request is verified against its issue and ready for a human to merge. Releases, changelogs, community support, and security advisories are later skills.

### Status labels

An issue has one status label:

| Label | Meaning |
| --- | --- |
| `status:inbox` | New. Manage has not sorted it. |
| `status:needs-info` | Waiting on a maintainer or reporter answer. |
| `status:blocked` | Waiting on another issue. |
| `status:ready` | One slice an agent can pick up. |
| `status:in-progress` | A branch exists and the work is underway. |
| `status:in-review` | A pull request is open. |

Closing the issue means done. There is no done label.

### Type labels

An issue has one type label:

| Label | Meaning |
| --- | --- |
| `type:spec` | Parent plan. Not implemented directly. |
| `type:slice` | One mergeable change. |
| `type:bug` | A defect. Developed like a slice. |

### What each phase writes

**setup-cycle** commits tooling in the target repository and creates labels on GitHub:

- `AGENTS.md` — GitHub is the work record, ADRs live in `docs/adr/`, the label meanings, and the command names
- `docs/adr/0000-template.md` — the ADR template, not a decision
- GitHub issue forms for spec, slice, and bug
- A pull request template
- The status and type labels

It does not rewrite the project README. It does not ask which tracker to use.

**plan** writes GitHub issues:

- One mergeable change becomes a `type:slice` issue with `status:ready`. The body has Problem, Acceptance, Non-goals, Modules, and related ADRs.
- Several mergeable changes become a `type:spec` parent plus `type:slice` children. Children link to their blockers with GitHub blocked-by. A child with an open blocker is `status:blocked`. A child with no open blocker is `status:ready`.

**manage** changes labels and posts a short comment when it needs information or closes a duplicate. It does not rewrite spec text. When every blocker on a `status:blocked` slice is closed, manage moves that slice to `status:ready`.

**architect** writes one ADR and the pull request that adds it. It calls `module-design`. It does not open a decision issue, and it does not open candidate issues for a decision that has no file yet.

- The file is `docs/adr/NNNN-title.md` on a branch. The pull request adds that file.

ADR sections are Status (`proposed`, `accepted`, or `superseded by ADR-NNNN`), Context, Decision, and Consequences. The next number is one higher than the highest existing `NNNN`, ignoring `0000-template.md`.

**develop** implements one `status:ready` issue of type `slice` or `bug`. It writes code and tests on a branch named `issue-<number>-<slug>`, opens a pull request that links the issue and names the ADRs it follows, and sets `status:in-review`. It stops before a QA verdict. A human merges.

**qa** posts one pull request comment:

- Verdict: `pass` or `fail`
- Acceptance criteria from the issue, each with evidence
- ADRs the change touches: followed, or a conflict
- The test command and its result

`fail` sets the issue back to `status:in-progress`. `pass` leaves `status:in-review` and says a human can merge. QA does not merge.

If `gh` is not authenticated, the phase stops and says so. If a slice has no acceptance criteria, the phase stops and manage moves that issue to `status:needs-info`.

## Skills

User-invoked skills set `disable-model-invocation: true`. Model-invoked skills omit that flag. Descriptions are third person and say what the skill does and when to use it.

### User-invoked

| Skill | Calls | Writes |
| --- | --- | --- |
| `setup-cycle` | `github-record` | Labels, issue forms, PR template, `AGENTS.md`, ADR template |
| `plan` | `spec-issue`, and `slice-work` only when there is more than one mergeable piece | Spec and slice issues |
| `manage` | `github-record` | Label changes and short comments |
| `architect` | `module-design` | An ADR file and the pull request that adds it |
| `develop` | `tdd` | Code, tests, and a pull request |
| `qa` | `review-pr` | A verdict comment on the pull request |

### Model-invoked

| Skill | Role |
| --- | --- |
| `github-record` | Label names, `gh` usage, and where text is allowed to live. Used whenever a skill creates or updates issues, pull requests, comments, or labels. |
| `spec-issue` | The spec issue body: Problem, Acceptance, Non-goals, Modules, ADRs. |
| `slice-work` | Child issues with GitHub blocked-by links. |
| `module-design` | The ADR file and the pull request that adds it. The ADR records the module boundary the change depends on. There is no decision issue. |
| `tdd` | Red-green-refactor for the issue being built, using the project's test command. One vertical slice. The instructions stay tool-neutral. |
| `review-pr` | The QA comment: acceptance criteria and ADRs. |

## Plugin repository

First setup of `attiasas/open-ai-dev` is the catalog and the backlog. Skill bodies come later, one GitHub issue at a time.

```text
open-ai-dev/
├── .cursor-plugin/plugin.json
├── .gitignore
├── AGENTS.md
├── LICENSE
├── README.md
├── SKILLS.md
├── docs/superpowers/specs/2026-11-11-oss-cycle-design.md
└── skills/README.md
```

`.cursor-plugin/plugin.json` fields: `name` (`oss-cycle`), `description`, `version` (`0.1.0`), `author.name` (GitHub login `attiasas`), `license` (`MIT`), `repository`, `keywords`. Skills are discovered from `skills/`. No rules, hooks, or MCP servers.

`skills/` has no `SKILL.md` files yet. A stub would appear as an invocable skill. A folder is added when that skill's issue is implemented.

`README.md` leads with Cursor install and Grok as the default model, then how Claude or Codex load the same `skills/` tree once skills exist.

`SKILLS.md` is the index: who invokes each skill, what it writes, and catalog status `planned`. Catalog status stays `planned` until that skill's `SKILL.md` exists. The GitHub label is the work state of the issue, separate from the catalog status.

`AGENTS.md` tells agents working on this plugin to implement one skill per issue, to keep user-invoked and model-invoked flags as specified above, and to treat GitHub issues as the backlog.

### Issue backlog

Twelve issues, one per skill. Each issue body is enough to implement that skill. Blocked-by edges:

| Issue | Blocked by |
| --- | --- |
| [#1](https://github.com/attiasas/open-ai-dev/issues/1) `github-record` | — |
| [#2](https://github.com/attiasas/open-ai-dev/issues/2) `setup-cycle` | #1 `github-record` |
| [#3](https://github.com/attiasas/open-ai-dev/issues/3) `spec-issue` | #1 `github-record` |
| [#4](https://github.com/attiasas/open-ai-dev/issues/4) `slice-work` | #1 `github-record` |
| [#5](https://github.com/attiasas/open-ai-dev/issues/5) `module-design` | #1 `github-record` |
| [#6](https://github.com/attiasas/open-ai-dev/issues/6) `tdd` | #1 `github-record` |
| [#7](https://github.com/attiasas/open-ai-dev/issues/7) `review-pr` | #1 `github-record` |
| [#8](https://github.com/attiasas/open-ai-dev/issues/8) `manage` | #1 `github-record` |
| [#9](https://github.com/attiasas/open-ai-dev/issues/9) `plan` | #3 `spec-issue`, #4 `slice-work` |
| [#10](https://github.com/attiasas/open-ai-dev/issues/10) `architect` | #5 `module-design` |
| [#11](https://github.com/attiasas/open-ai-dev/issues/11) `develop` | #6 `tdd` |
| [#12](https://github.com/attiasas/open-ai-dev/issues/12) `qa` | #7 `review-pr` |

`github-record` is `type:slice` and `status:ready`. Every other skill issue is `type:slice` and `status:blocked`.

## Out of scope

- Skill procedure text (`SKILL.md` bodies), until each issue is picked up
- Releases, changelogs, community support, security advisories
- A choice of issue tracker
- Glossary files
- Merging pull requests from QA
