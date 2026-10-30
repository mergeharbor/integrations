---
name: check-merge-readiness
description: Report whether a pull request is ready to merge according to MergeHarbor - the merge gate verdict, review score, open findings and change risk - and exactly what is left to do. Use when the user asks if a pull request (this branch's, or a number they give) can be merged, is ready, is blocked, or what is holding it up.
argument-hint: "[pull request number]"
allowed-tools: Bash(git remote get-url:*), Bash(git branch --show-current), Bash(git rev-parse:*), Bash(mh status:*), Bash(gh pr checks:*), Bash(gh pr view:*)
---

# Check merge readiness

This is read-only: it never merges, approves, overrides the gate or changes a finding.

## 1. Find the pull request

- Repository: `owner/name` from `git remote get-url origin`.
- Pull request: the number the user gave; otherwise `mh status --json` finds the pull request
  for this branch's commits. If there is none, say so and offer the `review-my-changes` skill
  for the local changes.

## 2. Gather the signals

With the MergeHarbor MCP server (server name `mergeharbor`; if its tools are missing, tell the
user to run `/mcp` and sign in to `mergeharbor`):

- `get_merge_gate` (repo, and pr or branch) first: `applies`, the `verdict` (`pass`, `warn`,
  `block` or `pending`), the `readiness` score (0-100), the one-line `title`, the counts of
  `blocking` and `warnings` items, and the `checklist` (each item has a `level` of `block` or
  `warn`, a `title` and a `detail`), plus `overridden` and `evaluated_at`. `more` counts
  checklist items left out.
- `get_pr_review` (repo, pr): the confidence score, summary, and whether the latest commit has
  been reviewed.
- `list_unaddressed` (repo, pr): open findings by severity.
- `get_change_risk` (repo, pr): risk score and level (low, medium, high, critical) with the
  factors behind it.

Only when the server has no `get_merge_gate` tool, or it returns `applies: false` (the merge
gate is not turned on for this repository), fall back to the code host. The gate, when it is
on, is posted as the check **MergeHarbor / merge gate**; on GitHub read it with:

```sh
gh pr checks <number> --json name,state,description,link
```

and pick the check whose name contains `merge gate`. On GitLab, Bitbucket, Gitea and Azure
DevOps it is a commit or pull request status named like `MergeHarbor / merge gate` or
`mergeharbor/merge-gate`. If there is no gate at all, say so and judge readiness from the
review, the open findings and the change risk alone; give the console link from
`get_pr_review` (`console_url`).

## 3. Report

Lead with the verdict in one line (`pass` = ready, `warn` = ready with warnings, `block` =
blocked, `pending` = waiting for review), with the readiness score:

- **Ready to merge**: every check in the policy passed.
- **Ready with warnings**: nothing blocks, but some checks need a look.
- **Blocked**: something must be fixed first.
- **Waiting for review**: the latest commit is still being reviewed; say when to check again.

Then, most important first: what blocks or warns (the gate's checklist, `block` items first;
if `more` is above 0, say how many more are in the console), open findings at
`critical` and `major` as `path:line — title`, the review score, and the change risk level with
its top factors. End with the next action for each blocking item, for example "fix 2 major
findings (use `explain-findings`)", "get 1 more approval" or "push to re-run the review".

If the gate was overridden, say by whom and why (from `title`). Do not try to re-run or override
the gate; tell the user to use the console if they want that.
