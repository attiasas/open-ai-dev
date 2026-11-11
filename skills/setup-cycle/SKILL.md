---
name: setup-cycle
description: Creates the cycle labels, issue forms, pull request template, AGENTS.md, and ADR template in one target repository. It is run once per repository before the other cycle commands.
disable-model-invocation: true
---

# Setup cycle

## Overview

Prepare one target repository so `plan`, `manage`, `architect`, `develop`, and `qa` can run. GitHub is the tracker. This command writes no spec, no decision, and no feature code.

**Violating the letter of the file list is violating the spirit of setup.** A maintainer, a director, a deadline, or time already spent does not add a file or a label.

**REQUIRED:** Read and follow `github-record` for every label name and for where text lives.

## When to use

Run this once in a target repository, before the other cycle commands.

Do not run it to choose a tracker, rewrite `README.md`, file a spec, record an architecture decision, or scaffold a feature.

## Procedure

1. Run `gh auth status`. If it fails, stop and say `gh` is not authenticated. Do not use the website, another API, a stand-in file, or `gh auth login` to finish in this turn.
2. Run `gh` in the target repository. Pass `--repo OWNER/REPO` when the checkout is a different repo.
3. Create each label in the table below that the repository does not already have. The description is the Meaning cell. If that exact name already exists, leave it and continue. Do not create any other label. Do not open an issue.
4. Write the files in the file table, using the bodies in this skill. If `AGENTS.md` already exists, insert the body at the top and keep the existing lines after it. If `docs/adr/0000-template.md` already exists, leave it. Create only paths that are missing.
5. Commit only the paths in the file table. Do not `git add -A`, `git add .`, or `git add README.md`.

```bash
git add -- AGENTS.md docs/adr/0000-template.md \
  .github/ISSUE_TEMPLATE/spec.yml \
  .github/ISSUE_TEMPLATE/slice.yml \
  .github/ISSUE_TEMPLATE/bug.yml \
  .github/ISSUE_TEMPLATE/decision.yml \
  .github/PULL_REQUEST_TEMPLATE.md
```

If a path was not missing, do not stage it.

Stop after that commit. Do not push, open a pull request, write a spec, or start another cycle command.

## Labels

Create these ten labels and no others. Names match `github-record`.

| Label | Meaning |
| --- | --- |
| `status:inbox` | New. Not sorted. |
| `status:needs-info` | Waiting on an answer. |
| `status:blocked` | Waiting on another issue. |
| `status:ready` | One slice an agent can pick up. |
| `status:in-progress` | A branch exists and the work is underway. |
| `status:in-review` | A pull request is open. |
| `type:spec` | Parent plan. Not implemented directly. |
| `type:slice` | One mergeable change. |
| `type:decision` | Points at an ADR. |
| `type:bug` | A defect. Developed like a slice. |

```bash
gh label create "status:inbox" --description "New. Not sorted."
gh label create "status:needs-info" --description "Waiting on an answer."
gh label create "status:blocked" --description "Waiting on another issue."
gh label create "status:ready" --description "One slice an agent can pick up."
gh label create "status:in-progress" --description "A branch exists and the work is underway."
gh label create "status:in-review" --description "A pull request is open."
gh label create "type:spec" --description "Parent plan. Not implemented directly."
gh label create "type:slice" --description "One mergeable change."
gh label create "type:decision" --description "Points at an ADR."
gh label create "type:bug" --description "A defect. Developed like a slice."
```

There is no `status:done`, `status:todo`, `status:doing`, `type:feature`, `type:story`, or `type:task`. A request to add one does not add it. Do not ask which tracker to use. Do not mention Jira, Linear, or another tracker as a choice.

## Files

| Path | What it is |
| --- | --- |
| `AGENTS.md` | GitHub is the work record, ADRs live in `docs/adr/`, the label meanings, and the command names |
| `docs/adr/0000-template.md` | ADR template. Not a decision |
| `.github/ISSUE_TEMPLATE/spec.yml` | Issue form for `type:spec` |
| `.github/ISSUE_TEMPLATE/slice.yml` | Issue form for `type:slice` |
| `.github/ISSUE_TEMPLATE/bug.yml` | Issue form for `type:bug` |
| `.github/ISSUE_TEMPLATE/decision.yml` | Issue form for `type:decision` |
| `.github/PULL_REQUEST_TEMPLATE.md` | Pull request template |

Do not create `config.yml`, `feature.yml`, `docs/specs/`, a numbered ADR, or feature source. Do not edit `README.md`. Adding a Cycle section is editing `README.md`. If this setup already changed `README.md`, restore that file to HEAD before the commit. An unstaged Cycle section is still a change. Do not stage `README.md`.

`docs/adr/0000-template.md` is not a decision. The Status section contains this sentence and nothing else: `proposed, accepted, or superseded by ADR-NNNN`. A blank Status heading is not the template. Do not set Status to `accepted`. Do not fill Context, Decision, or Consequences. Do not number it `0001`. Do not open a `type:decision` issue for it. A drafted `docs/adr/0001-use-github.md`, or any other numbered ADR created during this setup, is not committed. Delete that uncommitted draft. Do not delete a numbered ADR that was already committed before setup started.

### `AGENTS.md`

```markdown
# Work record

GitHub issues, pull requests, and comments are the work record. Specs, task breakdowns, and QA notes stay on GitHub. Do not put them in this repository.

Architecture decisions live in `docs/adr/NNNN-title.md`. `docs/adr/0000-template.md` is the template, not a decision.

## Labels

An issue has one status label:

| Label | Meaning |
| --- | --- |
| `status:inbox` | New. Not sorted. |
| `status:needs-info` | Waiting on an answer. |
| `status:blocked` | Waiting on another issue. |
| `status:ready` | One slice an agent can pick up. |
| `status:in-progress` | A branch exists and the work is underway. |
| `status:in-review` | A pull request is open. |

There is no `status:done`.

An issue has one type label:

| Label | Meaning |
| --- | --- |
| `type:spec` | Parent plan. Not implemented directly. |
| `type:slice` | One mergeable change. |
| `type:decision` | Points at an ADR. |
| `type:bug` | A defect. Developed like a slice. |

## Commands

`setup-cycle` runs once per repository before the other cycle commands. The other commands are `plan`, `manage`, `architect`, `develop`, and `qa`.
```

### `docs/adr/0000-template.md`

```markdown
# NNNN. Title

## Status

proposed, accepted, or superseded by ADR-NNNN

## Context

## Decision

## Consequences
```

### Issue forms

`spec.yml`, `slice.yml`, and `bug.yml` use the same body. Set `name`, `description`, and `labels` from this table. `description` is the Meaning cell.

| File | name | labels |
| --- | --- | --- |
| `spec.yml` | Spec | `["type:spec", "status:inbox"]` |
| `slice.yml` | Slice | `["type:slice", "status:inbox"]` |
| `bug.yml` | Bug | `["type:bug", "status:inbox"]` |

```yaml
name: Spec
description: Parent plan. Not implemented directly.
labels: ["type:spec", "status:inbox"]
body:
  - type: textarea
    id: problem
    attributes:
      label: Problem
    validations:
      required: true
  - type: textarea
    id: acceptance
    attributes:
      label: Acceptance
    validations:
      required: true
  - type: textarea
    id: non-goals
    attributes:
      label: Non-goals
    validations:
      required: false
  - type: textarea
    id: modules
    attributes:
      label: Modules
    validations:
      required: false
  - type: textarea
    id: adrs
    attributes:
      label: ADRs
    validations:
      required: false
```

`.github/ISSUE_TEMPLATE/decision.yml`:

```yaml
name: Decision
description: Points at an ADR. The decision text lives in docs/adr/, not in this issue.
labels: ["type:decision", "status:inbox"]
body:
  - type: input
    id: path
    attributes:
      label: ADR path
      description: Path under docs/adr/NNNN-title.md. Not 0000-template.md.
      placeholder: docs/adr/0001-title.md
    validations:
      required: true
```

### `.github/PULL_REQUEST_TEMPLATE.md`

```markdown
## Issue

Closes #

## ADRs
```

The pull request template does not replace the four issue forms. Commit both.

## Common mistakes

- Asking which tracker to use, then waiting.
- Editing `README.md` and calling the edit "not a rewrite."
- Creating `status:done` or `type:feature`.
- Skipping the blank ADR template, or committing `docs/adr/0001-use-github.md` as the setup decision.
- Skipping the four issue forms because a pull request template exists.
- Writing `docs/specs/` or feature code in the same commit.
- Doing none of the setup because the request also asks for those extras.

## Rationalization

| Excuse | Reality |
| --- | --- |
| "Agents only read README, so add the cycle there too." | Do not edit `README.md`. The cycle guide is `AGENTS.md`. |
| "I will not rewrite README. I will only add a section." | Adding a section edits `README.md`. Leave it unchanged. |
| "It's the workflow doc. I trimmed it to what's real tonight." | Restore `README.md` to HEAD. An unstaged Cycle section is still a change. |
| "The ADR is already written, so committing it is the honest finish." | Setup does not write a decision. The only ADR path is `docs/adr/0000-template.md`. |
| "An empty template is not a useful artifact." | The template is required. It stays empty. It is not `0001`. |
| "Skip the issue forms tonight. A pull request template is enough." | Commit the four issue forms and the pull request template. |
| "status:done is the one the maintainer asked for." | There is no `status:done`. Create the ten labels and no others. |
| "Ask which tracker before writing anything." | Do not ask. GitHub is the tracker. |
| "I will not invent labels, so I will not set up." | The ten names are listed. Create those. Refuse the spec, the feature code, and the extra labels. |
| "type:feature matches how we talk about the work." | There is no `type:feature`. Use `type:spec` and `type:slice`. |

## Red flags

Stop if you are about to ask which tracker to use, edit `README.md`, leave a Cycle section unstaged, create a label that is not in the table, commit a numbered ADR, leave Status blank, fill in `0000-template.md`, skip an issue form, add `docs/specs/` or feature code, or leave the repository without the file table.

Create the ten labels and commit the file table instead.
