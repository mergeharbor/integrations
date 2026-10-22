---
description: Show the MergeHarbor review of this branch's pull request
allowed-tools: Bash(mh status:*)
---

Run `mh status --json` and tell me the pull request, its confidence score, how many findings
are still open, and whether the latest commit has been reviewed. If there is no pull request
yet, say so and suggest `/mergeharbor-review` for the local changes.
