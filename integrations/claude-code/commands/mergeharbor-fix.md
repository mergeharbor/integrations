---
description: Fix MergeHarbor findings on this branch, then re-check until the review is clean
argument-hint: "[max rounds, default 3]"
allowed-tools: Bash(mh review:*), Bash(mh status:*), Bash(git status:*), Bash(git diff:*), Bash(git add:*), Read, Edit, Write
---

Use the `fix-findings` skill to fix the MergeHarbor findings for the current branch. Stop
after $ARGUMENTS rounds (3 if not given). Run the project's tests after each round, and do
not commit or push unless I ask.
