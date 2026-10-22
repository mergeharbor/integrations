# MergeHarbor integrations for coding agents

Plugins and prompts that bring [MergeHarbor](https://mergeharbor.com) code review into your coding agent. Everything here talks to your MergeHarbor account; sign in first with the [command-line client](https://mergeharbor.com/docs/cli): `brew install mergeharbor/tap/mh && mh login`.

| Agent | How to install |
|---|---|
| Claude Code | `/plugin marketplace add mergeharbor/integrations` then `/plugin install mergeharbor@mergeharbor` |
| Codex | `codex mcp add mergeharbor -- mh mcp`, and copy `integrations/codex/prompts/*.md` to `~/.codex/prompts/` |
| Cursor | see [`integrations/cursor`](integrations/cursor) |

Documentation: https://mergeharbor.com/docs/ide-and-agents
