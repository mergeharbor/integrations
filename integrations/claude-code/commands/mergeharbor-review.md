---
description: Review your local changes with MergeHarbor before pushing
argument-hint: "[--staged | --committed] [--deep | --max] [paths...]"
allowed-tools: Bash(mh review:*), Bash(git status:*), Bash(git diff:*), Read
---

Review the local changes in this repository with MergeHarbor.

1. Run `mh review --json $ARGUMENTS` from the repository root. If `mh` is not installed or not
   signed in, tell me how to fix it (`brew install mergeharbor/tap/mh`, then `mh login`) and stop.
2. Summarize the result: the confidence score, then each finding as `path:line — severity —
   title`, most severe first, with one sentence on why it matters.
3. Point out any configuration problems the review reported.
4. Ask whether I want you to fix the findings. Do not change code until I say so.
