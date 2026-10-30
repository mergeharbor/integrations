---
name: review-my-changes
description: Review the local changes in this repository with MergeHarbor (`mh review`) before committing or pushing, and summarize the findings. Use when the user asks for a MergeHarbor review of their changes, asks "is this ready to push?", or is about to commit, push or open a pull request.
argument-hint: "[--staged | --committed] [--deep | --max] [paths...]"
allowed-tools: Bash(mh review:*), Bash(mh whoami), Bash(git status:*), Bash(git diff:*), Bash(git rev-parse:*)
---

# Review my changes with MergeHarbor

MergeHarbor reviews the working tree with the same index, `.mergeharbor` configuration, team
rules and knowledge as a pull request review. Nothing is posted to the code host. Each review
uses review credits from the organization's budget, so run one review per request.

## 1. Pick the scope

Use the arguments the user gave (`$ARGUMENTS`). Otherwise choose:

- about to commit: `--staged`
- about to push: `--committed`
- anything else: no flag (branch commits plus staged, unstaged and new files)

Add paths to limit the review to them. Add `--deep` (or `--max`) only when the user asks for a
deeper review; deeper tiers cost more credits.

## 2. Run the review

From the repository root (`git rev-parse --show-toplevel`), run:

```sh
mh review --json --timeout 9m <scope flags> <paths>
```

Give the Bash call a 10-minute timeout. The exit code means:

| Code | Meaning |
|---|---|
| 0 | Done, nothing at or above `major` |
| 1 | Done, with a finding at or above `major` (the review still succeeded) |
| 2 | Usage error: fix the flags |
| 3 | Network, server or git error: show the message |

If the command fails before reviewing, tell the user the exact fix and stop:

- `mh` not found: `brew install mergeharbor/tap/mh` (or download it from
  https://github.com/mergeharbor/mh/releases)
- not signed in: `mh login`
- repository not connected to MergeHarbor: `mh onboard`
- hourly limit or budget reached: say so; do not retry in a loop

## 3. Summarize

From the JSON output, report:

1. The confidence score (`result.score`, out of 5), the tier (`result.tier`) and the credits
   used (`result.credits`).
2. Each finding in `result.findings`, most severe first (critical, major, minor, nit), as
   `path:line — severity — title`, followed by one sentence on why it matters. Mention when a
   finding has a suggested change.
3. Any `result.config_problems` (errors in `.mergeharbor` files).
4. The `console_url`, where the review is kept under Pull requests → Local (CLI).

If there are no findings, say so plainly with the score.

## 4. Stop and ask

Do not change code. Ask whether the user wants the findings explained or fixed; for that, use
the `explain-findings` skill.
