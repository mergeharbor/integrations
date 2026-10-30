# MergeHarbor for coding agents and editors

| Folder | What it is | Install |
|---|---|---|
| [`claude-code/`](claude-code) | Claude plugin: the MCP server and the `review-my-changes`, `explain-findings`, `ask-codebase`, `start-shipyard-task` and `check-merge-readiness` skills | `/plugin marketplace add mergeharbor/integrations`, then `/plugin install mergeharbor@mergeharbor` |
| [`codex/`](codex) | Codex MCP configuration, prompts and an `AGENTS.md` section | `codex mcp add mergeharbor -- mh mcp` |
| [`cursor/`](cursor) | Cursor MCP configuration, rule and commands | copy into `.cursor/` |

Everything here uses two pieces:

- the **MCP server** at `<console>/mcp` (findings, rules, reviews, change risk, the merge
  gate, knowledge bases, patches for suggested changes, marking findings addressed, Shipyard
  tasks), reached over HTTP with a browser sign-in, or through
  `mh mcp` with the CLI's stored key;
- the **`mh` command-line client** for reviews of local changes (`mh review`). See
  [docs/CLI.md](../docs/CLI.md).

The console's **Settings → Coding agents** page has these install commands with your
organization's address filled in. `go test ./integrations/`
validates the manifests.

This directory and the root `.claude-plugin/marketplace.json` are the only source of the
public `github.com/mergeharbor/integrations` repository: it holds a copy of both at the same
paths (`integrations/…` and `.claude-plugin/marketplace.json`), copied by hand when a change
is released; there is no export script. Raise
`version` in `claude-code/.claude-plugin/plugin.json` with every change to the plugin, and run
`claude plugin validate --strict integrations/claude-code` and `claude plugin validate --strict .`
before publishing.
