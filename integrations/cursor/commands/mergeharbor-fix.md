Fix the MergeHarbor findings for this branch. Collect them with the `mergeharbor` MCP tools
(`list_unaddressed` or `fix_all_plan`), or `mh review --json` when there is no pull request.
Fix the most severe first, run the tests, then re-run `mh review --json --fail-on minor`.
Repeat until it passes, at most three rounds, and summarize what changed and what is left.
