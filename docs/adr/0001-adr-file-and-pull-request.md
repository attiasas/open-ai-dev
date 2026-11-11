# 0001. ADR file and pull request

## Status

accepted

## Context

The cycle kept two records for one architecture decision: `docs/adr/NNNN-title.md` and a `type:decision` issue that linked to that path. The issue was an index, and a place to park a candidate before a file existed. The pull request that adds the file already holds the discussion, the review, and the merge.

## Decision

The decision text is `docs/adr/NNNN-title.md`. The pull request that adds that file is the GitHub record. There is no `type:decision` label and no decision issue. A candidate with no file yet does not get an issue. The next number is one higher than the highest existing ADR, ignoring `0000-template.md`.

## Consequences

`github-record` and `setup-cycle` list three type labels: `type:spec`, `type:slice`, and `type:bug`. Setup creates three issue forms. `module-design` writes the ADR and opens the pull request. `architect` calls that skill and does not open candidate issues. A slice names the ADR path in its ADRs section.
