---
name: fix-findings
description: Fix MergeHarbor review findings on the current branch and re-check until the review is clean (confidence 5/5 with nothing at or above minor). Use when the user asks to fix MergeHarbor comments, address review findings, or get a pull request to a passing MergeHarbor score.
---

# Fix MergeHarbor findings until the review is clean

Goal: every MergeHarbor finding on this branch is fixed (or deliberately set aside with a
reason), and a fresh review scores 5/5 with no finding at or above `minor`.

## 1. Collect the findings

- If the branch has a pull request, call the `list_unaddressed` MCP tool (it finds the pull
  request from the branch) or `fix_all_plan` for findings grouped by file, most severe first.
- Otherwise, or if the MCP server is unavailable, run `mh review --json` and read
  `result.findings`.

## 2. Fix them, most severe first

For each finding:
1. Read the code around `path:line` and the finding's explanation. If a suggested change is
   attached (`apply_fix` returns it as a patch), check it against the current file before
   applying it.
2. Make the smallest change that resolves the problem. Keep unrelated code as it is.
3. If you believe a finding is wrong, do not change the code: note it with your reasoning
   for the summary, so a person can dismiss it in MergeHarbor.

After the round, run the project's tests and linters and fix what you broke.

## 3. Re-check

Run `mh review --json --fail-on minor` on the result:
- Exit code 0 and `result.score` 5: done.
- Otherwise start the next round with the new findings.

Stop after 3 rounds (or the number the user gave). Each `mh review` uses review credits, so
do not loop beyond that; summarize what is left instead.

## 4. Report

List what you fixed (file and one line each), findings you set aside and why, the final
score, and the test results. When the user has pushed the fixes, call `mark_addressed` with
the fixed finding ids and the commit, so the pull request shows them as resolved.
