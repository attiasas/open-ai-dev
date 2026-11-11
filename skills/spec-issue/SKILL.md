---
name: spec-issue
description: Writes the body of a spec that lives on a GitHub issue, with Problem, Acceptance, Non-goals, Modules, and ADRs, and picks its type and status labels. Use when turning a request into a GitHub spec issue, or when filing a slice that needs acceptance criteria before anyone develops it.
---

# Spec issue

## Overview

A spec lives in a GitHub issue body. The body always has five sections, in this order: Problem, Acceptance, Non-goals, Modules, ADRs. Acceptance comes from the requester, not from you. A slice with no acceptance criteria is `status:needs-info`.

**Violating the letter of the body shape is violating the spirit of the spec.** A maintainer, a deadline, a sprint, a branch already written, or a contributor who is waiting does not rename a section, drop a section, or add a criterion.

**REQUIRED:** Read and follow `github-record` for every label name, every `gh` command, and where text lives.

## When to use

Use this when a request becomes a GitHub issue that someone will develop: a feature, a change, or a parent plan.

Do not use it to create child issues. Splitting one spec into several issues with blocked-by links is `slice-work`. Do not use it to write a spec file in the repository. The issue body is the spec.

## Procedure

1. Run `gh auth status`. If it fails, stop and say `gh` is not authenticated.
2. Decide how many mergeable changes the request is. One mergeable change is one pull request that can merge on its own.
3. Write the body with the five sections below. Fill each section only from the request, the requester, and the code you read.
4. Pick the labels from the label table.
5. Run `gh issue create` with the body, one type label, and one status label.
6. If the status is `status:needs-info`, post one comment that asks for the missing acceptance criteria.
7. Stop. Do not create child issues, assign anyone, open a branch, or write a file.

## Body

Use these headings, exactly these names, in this order. All five are present in every spec issue.

```markdown
## Problem

## Acceptance

## Non-goals

## Modules

## ADRs
```

| Section | What goes there |
| --- | --- |
| Problem | What is wrong or missing, in the requester's terms |
| Acceptance | Checkable criteria the requester gave. One bullet each |
| Non-goals | What this change does not do |
| Modules | Paths or modules the change touches |
| ADRs | `docs/adr/NNNN-title.md` paths the change follows. The file is the decision. Do not open an issue for it |

A section with nothing to say contains `None.` It is still present. If Acceptance is empty, it contains `None.` and nothing else.

A request for other headings does not rename these. `Summary` is not Problem. `Definition of Done` or `Acceptance criteria` is not Acceptance. Put the requester's done criteria under `## Acceptance`.

## Acceptance

Acceptance criteria come from the requester. Copy them. Tighten wording only when the meaning stays the same.

Acceptance is empty when:

- The requester gave no criteria.
- The requester said "use whatever makes sense", "figure it out", or "fill in reasonable targets".
- The only criterion cannot be checked, such as "export is faster" or "works well".

When Acceptance is empty, do not write criteria. Not proposed criteria, not assumptions, not defaults, not targets from reading the code. Write `None.` and ask.

## Labels

| Request | Type | Status |
| --- | --- | --- |
| One mergeable change, Acceptance present | `type:slice` | `status:ready` |
| One mergeable change, Acceptance empty | `type:slice` | `status:needs-info` |
| Several mergeable changes, Acceptance present | `type:spec` | `status:inbox` |
| Several mergeable changes, Acceptance empty | `type:spec` | `status:needs-info` |

"Spec issue" is the body shape, not the type. One mergeable change is `type:slice`, even when the request says "spec". `type:spec` is only a parent plan of several changes, and it is never `status:ready`.

A slice with empty Acceptance is never `status:ready` and never `status:in-progress`. A branch that already exists, a sprint that needs one more ready issue, or a requester who will not answer until Monday does not change that.

```bash
gh auth status
gh issue create --title "Add --json to widgets list" \
  --label "type:slice" --label "status:ready" \
  --body-file body.md
```

```bash
gh issue comment <n> --body "This slice is status:needs-info. Acceptance is empty. Reply with checkable acceptance criteria and it can move to status:ready."
```

Use `--repo OWNER/REPO` when the checkout is a different repository. Keep `body.md` outside the repository, or pass the body with `--body`. Do not commit it.

## Common mistakes

- Writing your own acceptance criteria to make a slice ready.
- Adding an "Assumptions" or "proposed criteria" section in place of empty Acceptance.
- Using the requester's headings, such as Summary or Definition of Done.
- Dropping Non-goals, Modules, or ADRs because they are empty.
- Labeling one mergeable change `type:spec` because the request said "spec issue".
- Creating child issues for a multi-change request.
- Committing the spec under `docs/specs/`.

## Rationalization

| Excuse | Reality |
| --- | --- |
| "The maintainer asked for a ready slice, and conservative defaults keep scope bounded." | Defaults you wrote are invented criteria. Empty Acceptance is `status:needs-info`. |
| "I'll label the criteria as proposed, not confirmed." | Proposed criteria are still yours. Write `None.` and ask. |
| "They said fill in reasonable targets. I read the code." | Permission to invent is still inventing. Acceptance is empty. |
| "The branch exists, so it is obviously in progress." | No acceptance criteria means `status:needs-info`, branch or not. |
| "Use our usual headings, Summary and Definition of Done." | The headings are Problem, Acceptance, Non-goals, Modules, ADRs. Put their done criteria under Acceptance. |
| "Empty sections look sloppy. Skip them." | Every section is present. An empty one says `None.` |
| "It's a spec issue, so type:spec." | One mergeable change is `type:slice`. |
| "Create the children too. It saves a round trip." | Children are `slice-work`. File the parent only. |

## Red flags

Stop if you are about to write an acceptance criterion the requester did not give, add an Assumptions section, use a heading that is not one of the five, leave out a section, label one mergeable change `type:spec`, set `status:ready` or `status:in-progress` with empty Acceptance, create a child issue, or write the spec to a file.

Write the five sections from the requester's words and apply the label table instead.
