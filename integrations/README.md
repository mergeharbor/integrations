# MergeHarbor for coding agents and editors

| Folder | What it is | Install |
|---|---|---|
| [`claude-code/`](claude-code) | Claude Code plugin: MCP server, `/mergeharbor-review`, `/mergeharbor-fix`, `/mergeharbor-status`, and the `fix-findings` and `review-before-push` skills | `/plugin marketplace add mergeharbor/integrations`, then `/plugin install mergeharbor@mergeharbor` |
| [`codex/`](codex) | Codex MCP configuration, prompts and an `AGENTS.md` section | `codex mcp add mergeharbor -- mh mcp` |
| [`cursor/`](cursor) | Cursor MCP configuration, rule and commands | copy into `.cursor/` |

Everything here uses two pieces:

- the **MCP server** at `<console>/mcp` (findings, rules, reviews, patches for suggested
  changes, marking findings addressed), reached over HTTP with a browser sign-in, or through
  `mh mcp` with the CLI's stored key;
- the **`mh` command-line client** for reviews of local changes (`mh review`). See
  [docs/CLI.md](../docs/CLI.md).

The console's **Settings → IDE & agents** page has these install commands with your
organization's address filled in. `go test ./integrations/`
validates the manifests.
