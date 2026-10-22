# MergeHarbor for Codex

1. Install and sign in to the command-line client:

   ```sh
   brew install mergeharbor/tap/mh && mh login
   ```

2. Add the MCP server to `~/.codex/config.toml` (see [`config.toml`](config.toml)), or run:

   ```sh
   codex mcp add mergeharbor -- mh mcp
   ```

3. Copy the prompts so `/mergeharbor-review` and `/mergeharbor-fix` are available:

   ```sh
   mkdir -p ~/.codex/prompts && cp prompts/*.md ~/.codex/prompts/
   ```

4. Optional: append [`AGENTS.md`](AGENTS.md) to your repository's `AGENTS.md` so Codex reviews
   before pushing and knows how to work through findings.
