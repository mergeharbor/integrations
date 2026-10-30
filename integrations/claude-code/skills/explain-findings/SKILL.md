---
name: explain-findings
description: Fetch the MergeHarbor review findings on a pull request (this branch's, or a number the user gives), explain each one against the local code, and fix them when the user asks. Use when the user asks about MergeHarbor comments or findings, why MergeHarbor flagged something, or to address or fix review feedback.
argument-hint: "[pull request number] [fix]"
allowed-tools: Bash(git remote get-url:*), Bash(git branch --show-current), Bash(git rev-parse:*), Bash(git status:*), Bash(git diff:*), Bash(mh status:*), Bash(mh review:*), Read, Grep, Glob
---

# Explain and fix MergeHarbor findings

This skill uses the MergeHarbor MCP server (server name `mergeharbor`). If its tools are not
available, the user has not signed in yet: tell them to run `/mcp`, choose `mergeharbor` and
sign in in the browser, then try again.

## 1. Find the pull request

- Repository: `owner/name` from `git remote get-url origin`.
- Pull request: the number the user gave; otherwise pass the current branch
  (`git branch --show-current`) and the tools find the open pull request from it.

## 2. Fetch the findings

- `get_pr_review` (repo, pr) for the score, summary and whether the latest commit is reviewed.
- `list_unaddressed` (repo, pr or branch) for the open findings, most severe first.
- `get_finding` (id) for one finding's full explanation, suggested change, `head_sha` and
  `fix_hint`.

If there is no pull request yet, say so and offer the `review-my-changes` skill for the local
changes instead.

## 3. Explain

Compare `head_sha` with `git rev-parse HEAD`. If they differ, say that the findings were made on
another commit and that line numbers may have moved.

For each finding, most severe first: read the code at `path:line` in the checkout, then give
`path:line — severity — title`, what is wrong, why it matters (the failure or risk it causes), and
the fix (the suggested change if there is one). Keep each explanation short. If you think a
finding is wrong, say why; do not argue it away silently.

## 4. Fix, only when the user asks

1. Call `fix_all_plan` (repo, pr or branch) for the order: grouped by file, most severe first.
2. For a finding with a suggested change, call `apply_fix` with the finding id and the file's
   current content (`file_content`). It returns a unified diff re-anchored on the local copy and
   writes nothing. Check the diff against the code, then make the change with Edit.
3. Otherwise make the smallest change that resolves the finding. Leave unrelated code alone.
4. Run the project's tests and linters, and fix what the change broke.
5. Re-check once with `mh review --committed --json --fail-on minor` if the user wants to
   confirm. Each review uses credits: at most 3 rounds of fix-and-review unless the user asks
   for more, then summarize what is left.

Do not commit or push unless the user asks.

## 5. Record the outcome, with the user's go-ahead

These tools change the pull request in MergeHarbor, so ask first:

- After the user has pushed the fixes: `mark_addressed` with the fixed finding ids and the
  commit SHA.
- For a finding the user agrees is wrong: `resolve_finding` with `action: dismissed` and the
  reason. Reasons teach MergeHarbor what not to flag.

## 6. Report

List what you fixed (file and one line each), findings left open and why, test results, and the
score of the last review if you ran one.
