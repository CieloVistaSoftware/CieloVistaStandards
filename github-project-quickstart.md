---
dewey: 000.4
id: github-project-quickstart
title: GitHub Project Quickstart
project: global
description: Version: 1.1.0 Last Updated: 2026-04-23 Owner: CieloVista Software Location: C:\Users\jwpmi\Downloads\CieloVistaStandards\github-project-quickstart.md
status: active
tags: [github, project, quickstart]
category: 800 — Global Standards
created: 2026-04-23
updated: 2026-04-27
version: 1.0.0
author: CieloVista Software
relativepath: github-project-quickstart.md
---
# GitHub Project Quickstart

**Version**: 1.1.0
**Last Updated**: 2026-04-23
**Owner**: CieloVista Software
**Location**: `C:\Users\jwpmi\Downloads\CieloVistaStandards\github-project-quickstart.md`

## Purpose

Three short paths for the most common starting points. Use this when you're setting up a new Kanban board, connecting local work to GitHub, or pulling an existing board+repo down locally. Written so you never have to click through the GitHub UI for the slow parts.

## Locked decisions (don't re-litigate)

- **Account**: personal account `CieloVistaSoftware`. No org migration until portfolio justifies it.
- **Boards**: one Projects v2 board per product, not one master board.
- **Commit convention**: `Fixes #NN` or `Closes #NN` in commit messages auto-closes GitHub issues on push.
- **Historical IDs**: `FIX-NNN` / `REG-NNN` stay in `data/fixes.json` for history; link from the GitHub issue body.

## One-time prerequisite — authenticate gh CLI

Everything below assumes this is done once per machine. If `gh auth status` prints "You are not logged into any GitHub hosts," run:

```powershell
gh auth login
```

Answers: GitHub.com → HTTPS → authenticate Git with your GitHub credentials (Yes) → Login with a web browser. Paste the one-time code, approve in browser, done.

Verify:

```powershell
gh auth status
```

Should show `Logged in to github.com as CieloVistaSoftware`.

## Path A — I have local code, nothing on GitHub yet

Starting point: a folder on disk with or without a local git repo. Goal: GitHub repo created, local work pushed, empty Projects board attached.

Run from inside the project folder:

```powershell
# 1. Init git locally if not already
git init
git add .
git commit -m "Initial commit"

# 2. Create the GitHub repo and push in one step
#    --source=. uses current folder, --push pushes the initial commit
gh repo create CieloVistaSoftware/PROJECT-NAME --private --source=. --push

# 3. Create a Projects v2 board under the personal account
gh project create --owner CieloVistaSoftware --title "PROJECT-NAME"

# 4. Note the project number printed by step 3 (e.g. 7), then link the repo
gh project link 7 --owner CieloVistaSoftware --repo PROJECT-NAME
```

You now have: remote repo, initial commit pushed, empty board linked. Open the board and add your first issue — or skip to the bulk-seed script below.

## Path B — Repo exists on GitHub, no board yet

Starting point: `CieloVistaSoftware/PROJECT-NAME` exists. Goal: new board attached to it.

```powershell
# 1. Create the board
gh project create --owner CieloVistaSoftware --title "PROJECT-NAME"

# 2. Link the existing repo to it (replace 7 with the project number from step 1)
gh project link 7 --owner CieloVistaSoftware --repo PROJECT-NAME
```

Two commands. Done.

## Path C — Board and repo exist on GitHub, I want them local

Starting point: repo and board both live on GitHub. Goal: working copy on disk.

```powershell
# From C:\dev  (or wherever you keep working copies)
gh repo clone CieloVistaSoftware/PROJECT-NAME
cd PROJECT-NAME
```

The board doesn't need a local sync step — it lives on github.com and you view it in the browser or via `gh project view 7 --owner CieloVistaSoftware --web`.

To pull updates later:

```powershell
git pull
```

## Bonus — bulk-seed a board from a json/csv

Don't click through the UI to file 30 issues. Drop them in with a script.

```powershell
# issues.csv with columns: title,body,labels
Import-Csv .\issues.csv | ForEach-Object {
    gh issue create `
        --repo CieloVistaSoftware/PROJECT-NAME `
        --title $_.title `
        --body  $_.body `
        --label $_.labels
}
```

Issues filed this way auto-appear on any Projects v2 board linked to the repo (if the board's filter accepts them).

## Critical — cmd.exe eats quotes around multi-word arguments

This will bite you every time. When `gh`, `git`, or any CLI is invoked from cmd.exe (including the vscode-claude-runner `powershell_command` tool, which actually wraps cmd.exe), double quotes around multi-word arguments are stripped before the target program sees them. This breaks any command that takes a title, commit message, issue body, or GraphQL query.

### Symptoms

```text
gh: unknown arguments ["Catalog" "description" "extractor" ...]
git: error: pathspec 'commit' did not match any file(s) known to git
gh: Argument 'login' has an invalid value (CieloVistaSoftware). Expected type 'String!'.
```

### The fix — always use a PowerShell script file

Write the command to a `.ps1` file, then invoke with `powershell -File`. PowerShell handles the quoting correctly, and cmd.exe only sees the script path.

Pattern:

```powershell
# 1. Write the script (via MCP filesystem write, or any editor)
$ErrorActionPreference = 'Stop'
Set-Location C:\dev\PROJECT-NAME
git commit -m 'Initial commit - extracted from monorepo'
```

Run it:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File C:\Users\jwpmi\AppData\Local\Temp\my-command.ps1
```

### For GitHub GraphQL queries specifically — use variables, never inline literals

Even inside a PowerShell script, embedded double quotes in GraphQL strings can still get mangled. Use GraphQL variables instead:

```powershell
# Wrong — inline "CieloVistaSoftware" will be stripped
$query = 'query { user(login: "CieloVistaSoftware") { ... } }'
gh api graphql -f query=$query

# Right — pass as a GraphQL variable
$query = 'query($login: String!) { user(login: $login) { ... } }'
gh api graphql -f query=$query -f login=CieloVistaSoftware
```

Use `-f` for string values and `-F` for typed values like integers (`-F num=5`).

### Use `--body-file` for issue/PR bodies

Never try to inline a multi-paragraph issue body. Write it to a file, pass `--body-file`:

```powershell
gh issue create `
    --repo CieloVistaSoftware/PROJECT-NAME `
    --title 'Short title here' `
    --body-file C:\Users\jwpmi\AppData\Local\Temp\issue-body.md `
    --label bug
```

## Critical — one manual step on the board

GitHub's GraphQL API does **not** expose a mutation to change a project's view layout from Table to Board. The default view created by `gh project create` is a Table. To make it an actual Kanban board, open the project in the browser and:

1. Click "View 1" dropdown at the top of the board
2. Choose "Board" layout
3. (Optional) rename the view to "Kanban" via the same dropdown

Takes 10 seconds. There is no CLI equivalent as of 2026-04-23.

Linking a repo with `gh project link` does **not** auto-add existing or future issues to the board. To add each issue explicitly:

```powershell
gh project item-add PROJECT_NUMBER --owner CieloVistaSoftware --url https://github.com/CieloVistaSoftware/REPO/issues/N
```

To enable auto-add for all future issues in a repo, this has to be set up once in the web UI: open board → `…` menu → Workflows → "Auto-add to project" → enable for the linked repo.

## Troubleshooting

- **`gh: command not found`** — install with `winget install GitHub.cli`, then reopen the terminal.
- **`gh auth login` hangs on browser step** — Ctrl+C, re-run, pick "Paste an authentication token" instead and use a PAT from github.com/settings/tokens with `repo` + `project` scopes.
- **`gh project create` says insufficient scopes** — your existing token is missing `project` scope. Run `gh auth refresh -s project`.
- **Issues aren't showing up on the board** — the board has a workflow filter. Open the board → `…` → Workflows → confirm "Auto-add to project" is enabled for the linked repo.

## See also

- `git_workflow.md` — branching, commit format, PR process
- `improved_dev_guidelines.md` — code review and quality standards
