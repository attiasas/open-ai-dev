---
name: slice-work
description: Splits a GitHub spec into a type:spec parent and type:slice child issues linked by real GitHub blocked-by relationships, and labels each child ready, blocked, or needs-info. Use when a spec has more than one mergeable piece and needs child issues agents can pick up in order.
---

# Slice work

## Overview

A spec with more than one mergeable piece becomes one `type:spec` parent and one `type:slice` child per piece. Each child records what it waits on with a GitHub blocked-by relationship. The relationship is what `manage` reads to move a slice to `status:ready`. A sentence in the body is not the relationship.

**Violating the letter of the links is violating the spirit of the order.** A maintainer, a sprint, a flaky API, or a bot that "reads prose" does not turn a `Depends on #N` sentence into a link, and does not make the parent ready.

**REQUIRED:** Read and follow `github-record` for every label name, every `gh` command, and where text lives. Read and follow `spec-issue` for the child body and its Acceptance rules.

## When to use

Use this when a spec has more than one mergeable piece. One mergeable piece is one pull request that can merge on its own.

Do not use it when the spec is one mergeable change. That is one `type:slice` issue from `spec-issue`. Steps inside one pull request, such as "parse the flag" and "update the help", are not pieces. A request for more slices, or a slice count per sprint, does not split one change.

Do not implement a child. Stop after the issues and links exist.

## Procedure

1. Run `gh auth status`. If it fails, stop and say `gh` is not authenticated.
2. Find the parent. If it does not exist, file it with `spec-issue` first: `type:spec`, `status:inbox` or `status:needs-info`. If it exists, read its labels. A parent labeled `type:slice` that has several pieces is relabeled `type:spec`.
3. List the pieces. For each piece, write down its Acceptance from the requester, its Modules, and which other pieces or existing issues must close first.
4. For each existing issue a piece waits on, read its state with `gh issue view <n> --json state --jq .state`. Do not take open or closed from the requester.
5. Order the pieces so every blocker is created before the child that waits on it.
6. Create each child in that order, with its body, labels, parent, and blocked-by in one `gh issue create`. Take the new number from the URL `gh issue create` prints. Do not predict numbers.
7. Read each child back and check its blocked-by list and parent match step 3.
8. For each child with status `status:needs-info`, post the needs-info comment.
9. Stop. Leave the parent's labels as `spec-issue` set them. Do not open a branch or write a file.

## Child body

Each child body is a `spec-issue` body: these five headings, these names, this order, every one present.

```markdown
## Problem

## Acceptance

## Non-goals

## Modules

## ADRs
```

Acceptance is that piece's criteria from the requester, copied. A piece with no criteria, "make it reasonable", "TBD", or only uncheckable criteria has Acceptance `None.` and nothing else. Do not write criteria for it.

Non-goals may name the sibling pieces this child does not do. You may mention the parent or a blocker in Problem. That sentence is context. It is not the link.

## Labels

Every child is `type:slice`. The parent is `type:spec` and is never `status:ready`. `status:ready` is one slice an agent can pick up, and the parent is not that slice.

Pick each child's status in this order. The first row that matches wins.

| Child | Status |
| --- | --- |
| Acceptance is `None.` | `status:needs-info` |
| At least one blocker is open | `status:blocked` |
| Every blocker is closed, or it has no blockers | `status:ready` |

A child with empty Acceptance is `status:needs-info` even when it has an open blocker. It still gets its blocked-by links. Do not label it `status:blocked`, because `manage` moves a blocked slice to ready when its blockers close, and it would become ready with no criteria.

A blocker that is a sibling you just created is open. A closed blocker still gets a link, and does not hold the child at `status:blocked`.

## Links

The blocked-by link is a GitHub issue relationship, created by `gh`. The parent link is a GitHub sub-issue.

```bash
gh auth status
gh issue view 30 --repo OWNER/REPO --json state --jq .state
gh issue create --repo OWNER/REPO \
  --title "Add widgets export --csv" \
  --label "type:slice" --label "status:blocked" \
  --parent 50 \
  --blocked-by 51,30 \
  --body-file /tmp/child-52.md
```

Add a link to an issue that already exists:

```bash
gh issue edit 52 --repo OWNER/REPO --add-blocked-by 51
gh issue edit 52 --repo OWNER/REPO --parent 50
```

Read it back:

```bash
gh issue view 52 --repo OWNER/REPO --json labels,parent,blockedBy
```

The read-back must list every blocker from step 3 under `blockedBy` and the parent under `parent`. If it does not, add the missing link with `gh issue edit`.

If a link command fails, retry once. If it fails again, stop. Do not create more children. Report which children exist, their labels, and which links are missing. Leave each existing child's status as the table gives it. Do not relabel a child `status:ready` because its link is missing, and do not finish the rest with prose.

The needs-info comment:

```bash
gh issue comment 53 --repo OWNER/REPO --body "This slice is status:needs-info. Acceptance is empty. Reply with checkable acceptance criteria. It is blocked by #51, so with criteria it is status:blocked until #51 closes."
```

Keep the body file outside the repository, or pass `--body`. Do not commit it.

## Common mistakes

- Filing only the parent and stopping because children "belong to another skill". Children are this skill.
- `Depends on #51` in the body with no blocked-by relationship.
- No `--parent`, so the child is not a sub-issue of the spec.
- Labeling the parent `status:ready` so bots "see the feature".
- Labeling a child with empty Acceptance `status:blocked` or `status:ready`.
- Labeling a child `status:blocked` when its only blocker is closed.
- Predicting issue numbers instead of reading them from `gh issue create`.
- Trusting the requester about whether a blocker is open.
- Creating a child before the sibling it waits on.
- Skipping the read-back.
- Slicing one mergeable change into steps to raise a slice count.

## Rationalization

| Excuse | Reality |
| --- | --- |
| "The relationships API is flaky. Write Depends on #N, everyone reads that." | A sentence is not the link. Create the blocked-by relationship. |
| "The link failed twice. Finish the rest with prose." | Stop and report the missing links. |
| "Blocked forever is worse. Mark them ready and let devs sort the order." | An open blocker is `status:blocked`. A missing link does not make a child ready. |
| "Make the parent ready so the bots see the whole feature." | The parent is `type:spec` and never `status:ready`. |
| "No criteria yet, put it at the front of the queue as blocked." | Empty Acceptance is `status:needs-info`, blocker or not. |
| "Just make the UI piece reasonable." | Permission to invent is still inventing. Acceptance is `None.` |
| "Everything with a dependency is blocked. Simplest rule." | A child whose blockers are all closed is `status:ready`. |
| "The children will be #51 to #53." | Read the number from `gh issue create`. |
| "Leadership counts slices. Split it into three." | One mergeable change is one slice. Do not use this skill. |

## Red flags

Stop if you are about to write `Depends on` instead of running `--blocked-by`, skip `--parent`, label the parent `status:ready`, label an empty-Acceptance child anything but `status:needs-info`, label a child `status:ready` while a blocker is open, write a criterion the requester did not give, create a child after a link failed twice, use a number you did not read from `gh`, or split one mergeable change.

Apply the label table and create the links instead.
