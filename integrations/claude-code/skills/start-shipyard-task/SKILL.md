---
name: start-shipyard-task
description: Hand a coding task for this repository and branch to Shipyard, MergeHarbor's coding agent, which plans the change, makes it in a sandbox, runs the tests and opens a reviewed pull request; then follow the task to its pull request. Use when the user asks to have Shipyard or MergeHarbor do a change, implement an issue in the background, or open a pull request for a task without doing it locally.
argument-hint: "<what to change>"
allowed-tools: Bash(git remote get-url:*), Bash(git branch --show-current), Bash(git status:*), Bash(git rev-parse:*), Bash(git ls-remote:*), Bash(mh whoami), Bash(mh status:*), Bash(gh pr list:*), Bash(gh pr view:*)
---

# Start a Shipyard task and follow it

Shipyard works on the code host's copy of the repository, not on this checkout. It drafts a
plan, waits for approval (unless the organization auto-approves), makes the change on a new
branch `mergeharbor/task-<id>`, runs the tests and lint in a sandbox, and opens a pull request
into the base branch that MergeHarbor reviews. It needs the Shipyard add-on, Shipyard turned on
by an administrator, the `pulls.fix` permission on the repository, and a repository connected
through the MergeHarbor GitHub App.

Each task uses credits, so confirm the estimate with the user before starting one.

## 1. Prepare the task

1. Repository: `owner/name` from `git remote get-url origin`.
2. Base branch: the current branch (`git branch --show-current`) unless the user names another.
   Check it exists on the remote (`git ls-remote --heads origin <branch>`) and that
   `git status` shows nothing unpushed the task depends on. If it does, tell the user Shipyard
   will not see those changes and ask them to push first.
3. Instructions: turn the request into clear instructions (10 to 8,000 characters): the change,
   the acceptance criteria, and the tests to add. Name up to 20 files or directories to focus
   on when you know them.
4. Tier: `standard` unless the user asks for more.

   | Tier | Typical task | Default cap |
   |---|---|---|
   | standard | 8 credits | 15 credits |
   | deep | 24 credits | 40 credits |
   | max | 60 credits | 100 credits |

## 2. Estimate, confirm, start

With the MergeHarbor MCP server (server name `mergeharbor`; if its tools are missing, tell the
user to run `/mcp` and sign in to `mergeharbor`):

1. Call `shipyard_estimate` with `repo`, `base_branch`, `instructions`, `paths`, `tier` and,
   if the user gave one, `credits_cap`. It makes no model calls and uses no credits. If the
   result has a `problem`, show its `message` and stop: it says what is missing (the Shipyard
   add-on, Shipyard turned off, the monthly budget, the code host connection) and who can fix it.
2. Show the user the repository, base branch, tier, instructions, the estimate
   (`credits_estimate`) and the most the task can use (`credits_cap`, `max_steps`,
   `max_minutes`), this month's usage (`month_credits_used` of `monthly_credit_budget`, if
   set), and whether the plan will start on its own (`auto_approve`) or wait for approval in
   the console. Ask them to confirm. Do not start a task without a clear yes.
3. Call `shipyard_start` with the same fields. It returns the `task_id`, `status`,
   `credits_cap`, `auto_approved` and `console_url`. Give the user the `console_url`. Unless
   the task was auto-approved, tell them the plan waits for their approval on that page:
   these tools never approve a plan.

If the server lists no `shipyard_estimate` and `shipyard_start` tools (an older installation),
open the task in the console with the instructions filled in instead:

1. Run `mh whoami`; it prints `Organization: <name> (<handle>)` and `Server: <url>`.
2. Give the user this link, with the instructions URL-encoded:
   `<url>/<handle>/shipyard/tasks/new?instructions=<instructions>`
3. Tell them to pick the repository, base branch and tier there (the form shows the credits
   estimate and cap), start the task, and approve the plan when it is ready.

## 3. Follow it

- `shipyard_get` (task_id) gives the `status`, `phase`, the `plan`, steps and credits used
  against the cap, the `checks`, the changed `files`, the `summary`, a `stop_reason` and
  `stop_message` when it stopped, and the `pull` request (number, url, review status and merge
  gate) once it opens.
- `shipyard_events` (task_id, `after` = the `last_id` you saw) gives new log lines only; call
  again with the returned `last_id` while `more` is true.
- When the pull request opens, report it, then use the `check-merge-readiness` skill for its
  review and merge gate, and the `explain-findings` skill if the review found problems.
- If the task stopped (a cap, the budget, failing checks), explain the `stop_message` and point
  to the `console_url`, where the user can decide what to do next.

Do not poll in a tight loop. Check when the user asks, or at most every few minutes for up to
30 minutes, then stop and summarize where the task is.
