# MergeHarbor for Cursor

1. MCP server: copy [`mcp.json`](mcp.json) to `.cursor/mcp.json` in your repository (or merge
   it into `~/.cursor/mcp.json`). Cursor opens the browser to sign in on first use. For a
   self-hosted installation, use your console's address.
2. Rule: copy [`rules/mergeharbor.mdc`](rules/mergeharbor.mdc) to `.cursor/rules/`.
3. Commands: copy [`commands/`](commands) to `.cursor/commands/` for `/mergeharbor-review` and
   `/mergeharbor-fix`.
4. The commands use the command-line client: `brew install mergeharbor/tap/mh && mh login`.
