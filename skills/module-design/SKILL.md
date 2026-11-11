---
name: module-design
description: Writes one architecture decision as docs/adr/NNNN-title.md and opens the pull request that adds that file. Use when recording a module boundary or other architecture decision.
---

# Module design

An architecture decision is one file, `docs/adr/NNNN-title.md`. The pull request that adds that file is the GitHub record. There is no decision issue.

**Violating the letter of the file is violating the spirit of the decision.** A maintainer, a board, or a half-written feature does not turn the pull request body into the decision, and does not add an issue.

**REQUIRED:** Read and follow `github-record` for `gh` auth, labels, and where text lives.

## When to use

Use this when a module boundary or other architecture decision has been chosen and needs to be recorded.

Do not use it to implement the feature (`develop`). Do not use it to open candidate issues for options that have no file yet. If no decision has been chosen, stop. Write nothing.

## Procedure

1. Run `gh auth status`. If it fails, stop and say `gh` is not authenticated. Do not use the website, another API, or `gh auth login` to finish in this turn.
2. List `docs/adr/`. Ignore `0000-template.md`. The next `NNNN` is one higher than the highest existing number, written as four digits. If no numbered ADR exists, use `0001`. Do not guess the number before listing the directory. Do not edit `0000-template.md` or replace an existing ADR to record a new decision.
3. Add `docs/adr/NNNN-title.md` and nothing else. Sections, in order:

```markdown
# NNNN. Title

## Status

proposed

## Context

## Decision

## Consequences
```

Status is `proposed`, `accepted`, or `superseded by ADR-NNNN`. Use `proposed` unless the requester said the decision is already accepted, or named the ADR this one supersedes. Do not leave Status blank.

The Decision section states the module boundary the change depends on, in the requester's words. Do not invent a second boundary. Do not put the decision text only in the pull request body.

4. Commit only that file. Open a pull request that adds it. The pull request body names the path under `## ADRs`. Do not open an issue. Do not create or apply `type:decision`. There is no such label.

```bash
ls docs/adr
git add -- docs/adr/0002-billing-owns-invoices.md
```

That example is the next file when `0001-adr-file-and-pull-request.md` is the only numbered ADR. Use the number from step 2, not `0002`, when the directory differs.

## Common mistakes

- Opening a `type:decision` issue, or three inbox issues for options that have no file.
- Putting the decision only in the pull request body.
- Numbering the file `0000`, or overwriting `0001`, because the directory was not listed.
- Committing feature code in the decision pull request.

## Rationalization

| Excuse | Reality |
| --- | --- |
| "Skip the file. An issue will show it on the board tonight." | The file is the text. The pull request is the GitHub record. There is no decision issue. |
| "The pull request body is the record, so no file is needed." | The pull request is the record because it adds the file. |
| "Survey with three issues and write the file next week." | No chosen decision means stop. Do not open candidate issues. |
| "0001 is the template slot. Overwrite it." | `0001` is an ADR if it exists. The next number is one higher. Ignore only `0000-template.md`. |
| "The mail sender is half written, so ship it with the decision." | The decision pull request adds the ADR file only. |

## Red flags

Stop if you are about to open a decision issue, create `type:decision`, write the decision only in a pull request body, skip listing `docs/adr/`, edit `0000-template.md`, overwrite an existing ADR, or include feature code in the decision pull request.

List the directory, write the next file, and open the pull request that adds it.
