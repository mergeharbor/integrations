---
description: Fix MergeHarbor findings and re-check until the review is clean
argument-hint: "[max rounds, default 3]"
---

Fix the MergeHarbor findings for this branch:

1. Get them from the `mergeharbor` MCP server (`list_unaddressed`, or `fix_all_plan` for a
   grouped plan); without a pull request, run `mh review --json` and use `result.findings`.
2. Fix them most severe first with the smallest correct change. If a finding looks wrong,
   leave the code and explain why in your summary.
3. Run the project's tests, then `mh review --json --fail-on minor`. Repeat until the exit
   code is 0 and the score is 5, for at most $ARGUMENTS rounds (3 if empty): each review uses
   credits.
4. Summarize what you fixed, what you set aside, the final score and test results. After the
   fixes are pushed, call `mark_addressed` with the fixed finding ids.
