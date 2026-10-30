# MergeHarbor for Claude

MergeHarbor is an AI code reviewer. This plugin brings it into Claude Code (and Claude on
claude.ai, the desktop app and Cowork): review your changes before you push, understand and fix
the findings on a pull request, ask questions about your codebase with sources, hand a task to
Shipyard, MergeHarbor's coding agent, and check whether a pull request is ready to merge.

It needs a MergeHarbor account (https://mergeharbor.com). Everything it does runs as you, with
exactly your permissions in your MergeHarbor organization.

## Install

In Claude Code:

```sh
/plugin marketplace add mergeharbor/integrations
/plugin install mergeharbor@mergeharbor
```

The first time Claude uses a MergeHarbor tool, run `/mcp`, choose `mergeharbor` and sign in in
your browser. You pick the organization and approve access on MergeHarbor's own consent page.

Local reviews use the MergeHarbor command-line client:

```sh
brew install mergeharbor/tap/mh     # or download from https://github.com/mergeharbor/mh/releases
mh login
```

### Self-hosted MergeHarbor

The plugin connects to MergeHarbor Cloud at `https://app.mergeharbor.com/mcp`. On a self-hosted
installation, point the `mh` client at your console and add MergeHarbor as an MCP server
through it instead:

```sh
export MERGEHARBOR_URL=https://mergeharbor.example.com   # your console's address
mh login
claude mcp add --scope user mergeharbor-self-hosted -- mh mcp
```

Then disable the plugin's own `mergeharbor` server in `/mcp`. The skills work with either server.

## What you get

| Skill | What it does |
|---|---|
| `/mergeharbor:review-my-changes` | Runs `mh review` on your working tree (`--staged`, `--committed`, `--deep`, or paths) and summarizes the score, findings and credits used. Claude also uses it when you are about to push. |
| `/mergeharbor:explain-findings` | Fetches the findings on your branch's pull request (or a number you give), explains each against your code, and fixes them when you ask, then marks them addressed after you push. |
| `/mergeharbor:ask-codebase` | Answers questions about the repository from MergeHarbor's generated docs, past reverts and incidents, and team rules, checked against your checkout, with sources. |
| `/mergeharbor:start-shipyard-task` | Prepares a Shipyard coding task for this repository and branch, confirms the cost with you, starts it, and follows it to its pull request. |
| `/mergeharbor:check-merge-readiness` | Reports the merge gate verdict, review score, open findings and change risk of a pull request, and what is left to do. |

The plugin also adds the MergeHarbor MCP server, with these tools:

- read-only: `list_findings`, `get_finding`, `list_unaddressed`, `fix_all_plan`, `apply_fix`,
  `get_pr_review`, `get_change_risk`, `get_merge_gate`, `list_rules`, `list_knowledge_bases`,
  `get_knowledge_page`, `search_knowledge_base`, `get_change_history`, `shipyard_estimate`,
  `shipyard_get` and `shipyard_events`;
- they change state in MergeHarbor (Claude asks before using them): `resolve_finding` and
  `mark_addressed` (change a finding's status), `add_rule` (proposes a rule an administrator
  approves) and `shipyard_start` (starts a Shipyard task, which uses credits, pushes a new
  branch and opens a pull request once its plan is approved).


## Cost

Local reviews and Shipyard tasks use credits from your organization's MergeHarbor budget, the
same as reviews in the product; organization budgets and caps apply. The skills run one review
per request, use the standard tier unless you ask for a deeper one, stop fix-and-review loops
after three rounds, and confirm a Shipyard task's tier and credits with you before starting it.
Reading findings, reviews, risk and docs through the MCP server uses no credits.

## Privacy and security

- **What it connects to.** The MCP server at `https://app.mergeharbor.com/mcp` (MergeHarbor
  Cloud), and the `mh` client, which talks to the same MergeHarbor API. The plugin sends nothing
  anywhere else. On GitHub, `check-merge-readiness` and `start-shipyard-task` also read pull
  request checks with your own `gh` command-line client.
- **What it sends.** `mh review` uploads the diff of your local changes (up to 4 MiB) for
  review; nothing is posted to your code host. `apply_fix` sends the content of the one file
  being fixed so the patch fits your copy. Other tools send only repository names, pull request
  numbers, branch names, finding ids, your questions' search terms and, for `shipyard_start`,
  the task instructions you confirmed.
- **Sign-in.** OAuth 2.1 with PKCE: you sign in through your browser and approve the client for
  one organization. Access tokens are short-lived, refresh tokens rotate, and you can revoke a
  session at any time in the MergeHarbor console. The plugin contains no credentials and reads
  none from your environment.
- **Permissions.** Every tool acts as you, with your role's permissions, single sign-on and IP
  allow list rules. Tools that change state are marked as such, and the skills ask before using
  them. The skills never commit, push, merge or override the merge gate.
- **Data handling.** MergeHarbor's handling of your code and data is described in the privacy
  policy: https://mergeharbor.com/legal/privacy and the terms: https://mergeharbor.com/legal/terms.
  Security contact: security@mergeharbor.com.

## Support


Email support@mergeharbor.com or see https://mergeharbor.com/docs/ide-and-agents.

## License

Use of this plugin is governed by the MergeHarbor Terms of Service; see [LICENSE](LICENSE).
