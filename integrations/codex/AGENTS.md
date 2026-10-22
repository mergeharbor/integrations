## Code review with MergeHarbor

- Before you push or open a pull request, run `mh review --committed` (or `mh review` for
  uncommitted work) and fix findings at `major` or above.
- To work through review comments on a pull request, use the `mergeharbor` MCP tools
  (`list_unaddressed`, `fix_all_plan`, `apply_fix`, `mark_addressed`), then re-run
  `mh review --fail-on minor` until it passes. Stop after three rounds.
- Review rules for this repository live in `.mergeharbor/` folders; follow them when you
  write code.
