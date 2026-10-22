# MergeHarbor for Claude Code

Review changes before you push, fix review findings until the review is clean, and check a
branch's review without leaving Claude Code.

## Install

```sh
/plugin marketplace add mergeharbor/integrations
/plugin install mergeharbor@mergeharbor
```

The plugin connects Claude Code to the MergeHarbor MCP server at
`https://app.mergeharbor.com/mcp`; the first use opens the browser to sign in. For a
self-hosted installation, change the URL in `.mcp.json` to your console's address.

The slash commands use the `mh` command-line client for local reviews:

```sh
brew install mergeharbor/tap/mh && mh login
```

## What you get

| | |
|---|---|
| `/mergeharbor-review` | Reviews your local changes (`--staged`, `--committed`, `--deep`, paths) |
| `/mergeharbor-fix` | Fixes the findings on this branch, runs your tests, and re-checks until the review is clean (3 rounds by default) |
| `/mergeharbor-status` | The review of this branch's pull request |
| `fix-findings` skill | Used automatically when you ask to fix MergeHarbor comments |
| `review-before-push` skill | Used when you are about to push or open a pull request |
| MCP tools | Findings, rules, reviews, patches for suggested changes, marking findings addressed |
