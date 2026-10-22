---
name: review-before-push
description: Review local changes with MergeHarbor before committing or pushing. Use when the user is about to push, open a pull request, or asks "is this ready?", and after finishing a sizeable change.
---

# Review changes before pushing

1. Pick the scope:
   - about to commit: `mh review --staged --json`
   - about to push: `mh review --committed --json`
   - otherwise: `mh review --json` (branch commits plus uncommitted edits)
   Add `--deep` only when the user asks for a thorough review; it costs more credits.
2. If `mh` reports that the repository is not connected or you are not signed in, tell the
   user the exact fix (`mh onboard`, or `mh login`) and stop.
3. Report the confidence score and the findings, most severe first, as
   `path:line — severity — title`, each with one sentence of explanation.
4. Offer to fix them with the `fix-findings` skill. Do not change code without the user's
   go-ahead.
