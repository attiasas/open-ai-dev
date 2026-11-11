---
name: github-record
description: Defines the shared label set, gh usage, and where cycle text lives. Use when creating or updating GitHub issues, pull requests, comments, or labels for this cycle.
---

# GitHub record

GitHub issues, pull requests, and comments are the work record. One issue has one status label and one type label. Closing an issue means done.

**Violating the letter of the label set is violating the spirit of the record.** A maintainer, a board, or a deadline does not add a label or a docs path.

## When to use

Use this before creating or updating an issue, pull request, comment, or label for this cycle.

Label creation, issue forms, and templates are `setup-cycle`. The spec issue body is `spec-issue`.

## Authenticate first

Run `gh auth status` before any GitHub write. If it fails, stop and say `gh` is not authenticated. Do not use the website, another API, a stand-in file, or `gh auth login` to finish the update in this turn.

Run `gh` in the repository you are changing. Pass `--repo OWNER/REPO` when the checkout is a different repo.

## Labels

Status, exactly one:

| Label | Meaning |
| --- | --- |
| `status:inbox` | New. Not sorted. |
| `status:needs-info` | Waiting on an answer. |
| `status:blocked` | Waiting on another issue. |
| `status:ready` | One slice an agent can pick up. |
| `status:in-progress` | A branch exists and the work is underway. |
| `status:in-review` | A pull request is open. |

Type, exactly one:

| Label | Meaning |
| --- | --- |
| `type:spec` | Parent plan. Not implemented directly. |
| `type:slice` | One mergeable change. |
| `type:bug` | A defect. Developed like a slice. |

There is no `status:done` and no `type:decision`. No other status or type label exists.

Replace a label. Remove only a label the issue already has, then add the one new label. `gh issue edit --remove-label` fails when the label is absent.

```bash
gh issue view 17 --json labels --jq '.labels[].name'
gh issue edit 17 --remove-label "status:ready" --add-label "status:in-progress"
```

A `type:slice` or `type:bug` with no acceptance criteria is not ready. Its only status is `status:needs-info`. Do not leave it in progress while you ask, and do not invent criteria.

## Where text lives

| Text | Where |
| --- | --- |
| Specs, slices, task breakdowns | Issue body |
| QA notes, need-info, duplicates | Comment on the pull request or issue |
| Architecture decision | `docs/adr/NNNN-title.md`. The pull request that adds the file is the GitHub record |

Do not open an issue for a decision. The file is the text. The next `NNNN` is one higher than the highest ADR, ignoring `0000-template.md`.

Do not put specs, slices, or QA notes in the repo. `docs/specs/` and `docs/qa/` are not a record. Done is `gh issue close <n>` after the QA comment. Remove the status label first. A closed issue has no status label. Do not add a label for done, and do not leave `status:in-review` on it.

## Operations

| Do | Command |
| --- | --- |
| Check auth | `gh auth status` |
| Read labels | `gh issue view <n> --json labels --jq '.labels[].name'` |
| Replace one label | `gh issue edit <n> --remove-label "<old>" --add-label "<new>"` |
| Comment | `gh issue comment <n> --body "..."` or `gh pr comment <n> --body "..."` |
| Done | `gh issue close <n>` |
| Open | `gh issue create --title "..." --body "..." --label "<type>" --label "<status>"` |

## Common mistakes

- Two labels from the same table.
- `status:done`, or `status:in-review` left on an issue you are closing.
- Spec or QA text committed under `docs/`.
- `status:ready` or `status:in-progress` with no acceptance criteria.
- A `type:decision` issue, or a decision whose text lives only in a pull request body.

## Rationalization

| Excuse | Reality |
| --- | --- |
| "Closed means abandoned. Add status:done and leave it open." | Closing means done. There is no done label. |
| "Put the QA note in docs/qa so it is versioned." | QA notes are comments. |
| "Leave it in progress and ask for acceptance criteria." | No acceptance criteria means `status:needs-info`. |
| "gh is logged out. Use the website, a file, or gh auth login, then continue." | Stop. Say `gh` is not authenticated. |
| "Open a type:decision issue so the backlog can find the ADR." | The pull request that adds the file is the GitHub record. There is no decision issue. |
| "Add type:decision and type:spec, or two status labels." | There is no `type:decision`. Exactly one type label, from the table. |

## Red flags

Stop if you are about to add a label that is not listed, leave two status or two type labels, commit spec or QA text under `docs/`, open a decision issue, or record a decision without the ADR file, write to GitHub after `gh auth status` failed, or set `status:ready` or `status:in-progress` with no acceptance criteria.

Apply the tables instead.
