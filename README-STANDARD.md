---
dewey: 000.4
id: cielovista-readme-standard
title: CieloVista README Standard
project: global
description: Every .md file named README.md (or .README.md) must conform to one of three templates depending on its scope. The compliance tool checks these rule…
status: active
tags: [readme, standard, cielovista]
category: 800 — Global Standards
created: 2026-03-14
updated: 2026-04-27
version: 1.0.0
author: CieloVista Software
relativepath: README-STANDARD.md
---
# CieloVista README Standard

> **Global standard — applies to ALL CieloVista projects.**
> Location: `C:\Users\jwpmi\Downloads\CieloVistaStandards\README-STANDARD.md`
> Version: 1.0.0

---

## Overview

Every `.md` file named `README.md` (or `*.README.md`) must conform to one of three
templates depending on its scope. The compliance tool checks these rules automatically.

---

## Three README Types

### Type 1 — PROJECT README  (`README.md` at project root)


**Required sections (in this order):**

```markdown
# Project Name
One-line tagline describing what the project does.

> **Location:** `relative/path/to/README.md`

## What it does
2–5 sentences. What problem does it solve? Who uses it?

## Quick Start
Minimum commands to get running. No fluff.

## Architecture
High-level tech stack + key design decisions. Max 10 lines.

## Project Structure
Directory tree showing top-level folders only. Annotate each.

## Common Commands
The 5–10 commands a developer will run most often. Table or code block.

## Prerequisites
What must be installed. Bullet list.

## License
One line: "Copyright (c) YYYY CieloVista Software"
```

**Rules:**
- Maximum 300 lines. If longer, extract details into `docs/` files.
- No duplicate content from CLAUDE.md.
- No session management instructions (those live in CLAUDE.md).
- First heading must be `# Project Name` — no subtitles, badges, or preamble before it.

---

### Type 2 — FEATURE README  (`*.README.md` inside a project)

Used for individual feature files in cielovista-tools, component files in wb-core, etc.

**Required sections (in this order):**

```yaml
# feature: filename.ts   (or component-name / module-name)

## What it does
One paragraph. Describe the single responsibility of this file.

## Commands  (skip if no commands)
Table: Command ID | Title | Keybinding

## Settings  (skip if no settings)
Table: Key | Type | Default | Description

## Internal architecture
ASCII diagram or bullet tree showing the call flow.

## Manual test
Numbered steps to verify it works end-to-end.
```

**Rules:**
- Maximum 150 lines.
- Must start with `# feature:` or `# component:` or `# module:`.
- No copying from the source file's JSDoc — write it fresh for a human reader.

---

### Type 3 — STANDARD / GUIDE README  (docs in `CieloVistaStandards/` or `docs/`)

Used for TIER1-LAWS, coding standards, workflow guides, etc.

**Required sections (in this order):**

```yaml
# Document Title

> One-line summary of what this standard covers.

## Purpose
Why does this standard exist? What problem does it prevent?

## Rules
Numbered list. Each rule is one clear sentence. No prose.

## Examples  (skip if not applicable)
Show correct and incorrect usage side by side.

## Related Documents
Links to other standards or guides this one connects to.

## Changelog
- vX.Y.Z (YYYY-MM-DD): What changed
```

**Rules:**
- Must include the blockquote summary line directly after the title.
- Rules section must use a numbered list — no bullets.
- Must include a Changelog section.

---

## Universal Rules (all README types)

1. First line must be a `#` heading — no blank lines, badges, or comments before it.
2. No HTML tags except in tables.
3. No inline styles.
4. Headings must be in proper hierarchy (`#` → `##` → `###`) — never skip levels.
5. Code blocks must specify a language (`powershell`, `typescript`, `json`, etc.).
6. No heading may appear twice in the same file.
7. All internal links must point to files that actually exist.
8. Copyright notice required in project READMEs — not required in feature READMEs.

---

## Compliance Scoring

The compliance tool scores each README from 0–100:

| Check | Points |
|---|---|
| Correct type detected | 5 |
| First line is `#` heading | 10 |
| All required sections present | 40 |
| Sections in correct order | 10 |
| Within line limit | 10 |
| No duplicate headings | 5 |
| Code blocks have language tags | 10 |
| No skipped heading levels | 10 |

Score < 60 = **Non-compliant** (tool will offer to fix)
Score 60–79 = **Partial** (tool will show what's missing)
Score 80–100 = **Compliant**

---

## Auto-fix Capability

The compliance tool can auto-fix:
- Add missing required section stubs with placeholder text
- Fix first-line non-heading issues
- Add language tags to bare code blocks
- Sort sections into correct order (with user confirmation)

The tool will NOT auto-fix:
- Content within sections (only stubs are added)
- Line limit violations (reported only)
- Duplicate headings (reported, user must resolve)
