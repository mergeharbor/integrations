---
name: ask-codebase
description: Answer questions about how this repository works, using MergeHarbor's generated knowledge base (architecture, modules, flows, conventions, past reverts and incidents) and team rules, checked against the local code, with sources. Use when the user asks how something works, where something lives, why code is the way it is, what is risky to change, or what the team's conventions are.
argument-hint: "<question>"
allowed-tools: Bash(git remote get-url:*), Bash(git rev-parse:*), Bash(git log:*), Read, Grep, Glob
---

# Answer questions about the codebase

MergeHarbor writes and maintains documentation for each indexed repository. This skill reads
it through the MergeHarbor MCP server (server name `mergeharbor`) and checks it against the
code in the checkout. If the MCP tools are not available, tell the user to run `/mcp`, choose
`mergeharbor` and sign in, and meanwhile answer from the local code alone, saying so.

Repository: `owner/name` from `git remote get-url origin`.

## 1. Gather context

Use only the tools that fit the question; all of them are read-only:

- `list_knowledge_bases`: whether this repository has generated docs, their status and page
  count. If it has none, say so, answer from the local code, and mention that docs appear in
  the MergeHarbor console under Memory → Knowledge base → Generated once the repository is
  indexed.
- `get_knowledge_page` (repo): the index (architecture, glossary, where files live, the list of
  pages); then (repo, page) for a module, a flow, conventions or recipes.
- `search_knowledge_base` (query): full-text search across the pages, for names and terms.
- `get_change_history` (repo, optional paths): past reverts, rollbacks, hotfixes and incident
  fixes, for "is this risky?" or "why is this like this?" questions.
- `list_rules` (repo): the team's review rules, for convention questions.

## 2. Verify in the code

Every page lists its sources and the commit it was written from. Open the files the answer
depends on with Read or Grep and confirm the claim. When the page and the code disagree, trust
the code, say that the page may be out of date, and name the commit it was written from.

## 3. Answer

Answer the question directly first, then the detail. Cite every claim:

- code as `path:line` (or `path` for a whole file) from the checkout,
- knowledge base pages by their title,
- past incidents and reverts by the commit or pull request the history names.

If neither the docs nor the code answer the question, say what you checked and what is
missing. Do not guess.
