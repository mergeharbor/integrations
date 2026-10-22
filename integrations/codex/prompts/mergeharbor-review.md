---
description: Review local changes with MergeHarbor before pushing
argument-hint: "[--staged | --committed] [--deep] [paths...]"
---

Run `mh review --json $ARGUMENTS` in the repository root. Report the confidence score and each
finding as `path:line — severity — title`, most severe first, with one sentence on why it
matters, plus any configuration problems. Ask before changing any code.
