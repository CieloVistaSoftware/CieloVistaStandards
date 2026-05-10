---
dewey: 000.4
id: cielovista-doc-contract
title: CieloVista Doc Contract
project: global
description: Every .md document in a registered CieloVista project must conform to this contract. Enforced by validatedoc and listdocviolations MCP tools.
status: active
relativePath: doc-contract.md
---
# CieloVista Doc Contract

Every `.md` document in a registered CieloVista project must conform to this contract.
Enforced by `validate_doc` and `list_doc_violations` MCP tools.

---

## CieloVista Document Classification (CDC)

The CDC is a Dewey-inspired classification system for software project documentation.
It is the permanent numbering scheme for all CieloVista projects. It does not change.

### Structure

```json
{hundreds}.{sub-code}
```

- **Hundreds** — project identity, assigned once in `project-registry.json`, never reused
- **Sub-code** — document type, `.1`–`.9` fixed taxonomy, `.10`+ for project extensions
- **`docid`** — unique sequential ID, separate from classification, never changes
- **`category:`** — always derived from `dewey:`, never edited by hand

### Growth

- Hundreds support up to 9900 — room for 99 projects at 100-unit spacing
- Sub-codes `.10`–`.99` available per project for custom categories
- Thousands digit reserved for future multi-organization use
- Letters (`.1a`, `.1b`) available for sub-categories within a type

---

## Required Front-Matter

```yaml
---
docid: 150.39
dewey: 150.1
id: my-doc-slug
title: Human-Readable Title
project: cielovista-tools
description: One-line description, 200 chars max
status: active
category: 150.1 — Components / Features
---
```

**Full stable identifier:** `{dewey}.{id}` — e.g. `150.1.copilot-open-suggested-file`

This string is permanent. It survives folder renames, file moves, and filename changes.

---

## Field Rules

| Field | Required | Rules |
|---|---|---|
| `docid` | yes | `{hundreds}.{sequential}` — unique, never reused |
| `dewey` | yes | `{hundreds}.{sub-code}` — hundreds must match project prefix |
| `id` | yes | lowercase-kebab-case, 3–50 chars, unique within dewey, never reused |
| `title` | yes | non-empty string |
| `project` | yes | must match a `name` in `project-registry.json` |
| `description` | yes | non-empty, ≤ 200 chars |
| `status` | yes | one of: `active`, `draft`, `archived` |
| `category` | yes | computed from `dewey` — `{dewey} — {label}`, never set manually |
| `tags` | no | array of strings |
| `owner` | no | optional author/team string |
| `created` | no | ISO 8601 date |
| `updated` | no | ISO 8601 date |
| `version` | no | semver string |
| `deprecated` | no | boolean — marks doc as superseded |

---

## Project Prefixes (Dewey Hundreds)

| Prefix | Project |
|---|---|
| `000` | Global / CieloVistaStandards |
| `100` | vscode-claude |
| `150` | cielovista-tools |
| `200` | wb-core |
| `300` | DiskCleanUp |
| `400` | SnapIt |
| `500` | ANeedToKnow |
| `600` | BrowserKeeper |
| `700` | ClaudeAIWebSiteBuilder |
| `800` | VSCode-extensions |
| `900` | DrAlex |
| `1000` | ai |
| `1100` | company |
| `1200` | language |
| `1300` | protocols |
| `1400` | samples |
| `1500` | settings |
| `1600` | tooling |
| `1700` | templates |
| `1800` | JesusFamilyTree |

New projects are assigned the next available hundred in `project-registry.json` at promote time.
Prefixes are permanent — once assigned they never change, even if a project is retired.

---

## Sub-Codes (Standardized Across All Projects)

| Sub-code | Category | Description |
|---|---|---|
| `X.1` | Components / Features | Individual features, UI components, commands, modules |
| `X.2` | Architecture | System design, data flow, integration patterns, ADRs |
| `X.3` | Testing | Test strategy, test plans, known failures, coverage policy |
| `X.4` | Policy & Standards | Coding standards, review rules, contribution guidelines |
| `X.5` | AI Coordination | Prompts, agent instructions, CLAUDE.md guidance, AI tooling |
| `X.6` | Release & Deployment | Release notes, deployment steps, versioning, changelogs |
| `X.7` | Getting Started | Onboarding, setup, quickstart, prerequisites |
| `X.8` | API / Reference | API docs, type reference, schema definitions, CLI reference |
| `X.9` | Meta | Roadmap, status docs, planning, session notes, parking lots |

Sub-codes `.10`, `.11`, etc. are available for project-specific extensions.
These must be documented in the project's own `CLAUDE.md`.
Projects without a particular category simply have no docs at that sub-code.

---

## Category Rule

`category:` is always `{dewey} — {label}`. It is computed, never typed by hand.

Examples:
- `dewey: 150.1` → `category: 150.1 — Components / Features`
- `dewey: 300.5` → `category: 300.5 — AI Coordination`
- `dewey: 1800.9` → `category: 1800.9 — Meta`

The Doc Intelligence scanner flags any doc where `category:` does not match `dewey:`.

---

## Identity Rules

- **`docid` is permanent.** Once assigned it never changes.
- **`id` is permanent.** Once assigned it never changes.
- **Retired ids are not reused.** If a doc is deleted, its id is gone forever.
- **Uniqueness.** No two docs may share the same `{dewey}.{id}`.
- **Renaming** requires an entry in `dewey-aliases.json` mapping old → new before the rename is committed.

---

## Validator Checks

A conforming doc must pass all of the following:

1. All required front-matter fields are present and non-empty
2. `dewey` matches `\d+\.\d+` and the hundreds match the project's assigned prefix
3. `category` equals `{dewey} — {taxonomy-label}`
4. `docid` matches `\d+\.\d+` and the hundreds match the project's assigned prefix
5. `id` matches `^[a-z0-9][a-z0-9-]{1,48}[a-z0-9]$`
6. `project` matches a registered project name in `project-registry.json`
7. `description` is ≤ 200 characters
8. `status` is one of `active`, `draft`, `archived`
9. `{dewey}.{id}` is unique across all scanned docs — no collisions
10. Every `![alt](path)` image reference resolves to an existing file

---

## Link Resolution

`get_catalog` returns identity + current path. Links are built at render time — never stored as pre-baked paths.

| Target | URL pattern |
|---|---|
| VS Code | `vscode://file/{currentFilePath}` (forward slashes) |
| Viewer preview | `/doc/{dewey}.{id}` — stable, survives file moves |
| GitHub | `{project.githubUrl}/blob/main/{currentPath}` — only if project has `githubUrl` |

---

## Migration

Old identifiers using `subject:` are treated as `dewey:` equivalents during migration.
Aliases are preserved in `dewey-aliases.json`:

```json
{
  "1400.005": "200.2.old-architecture-doc"
}
```

Resolution checks aliases first, then current identities. Old links keep working.
Migration is human-in-the-loop via the `migrate_dewey` MCP tool.
