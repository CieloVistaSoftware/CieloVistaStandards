# README

﻿---
dewey: 000.4
id: cielovistastandards
title: CieloVistaStandards
project: global
description: The central hub of the CieloVista Software ecosystem.
status: active
tags: [readme, cielovistastandards, what]
category: 800 — Global Standards
created: 2026-04-22
updated: 2026-04-27
version: 1.0.0
author: CieloVista Software
relativepath: README.md
---
# CieloVistaStandards
The central hub of the CieloVista Software ecosystem.

> **Location:** `C:\Users\jwpmi\Downloads\CieloVistaStandards`

## What it does

This folder is the authoritative source of truth for all CieloVista Software development. It serves two roles simultaneously:

1. **Runtime tool registry** — `project-registry.json` is actively read by the `cielovista-tools` MCP server at runtime to discover and scan every registered project. This is what enables cross-project commands like `list_cvt_commands`, `find_symbol`, and the doc catalog to work across the entire ecosystem.

2. **Governance layer** — Defines coding standards, AI assistant behavior rules, and workflows that every project defers to. All project-level `CLAUDE.md` files point back here.

> Currently, MCP tools only scan paths registered in `project-registry.json`. The registry is the gate — but the scanning logic is plain filesystem traversal and can be extended to accept arbitrary folder paths.

## Contents

| File | Purpose |
|---|---|
| `project-registry.json` | Master list of all CieloVista projects and their local paths |
| `copilot-rules.md` | AI assistant behavior rules — applies to all projects |
| `CLAUDE.md` | Session startup instructions for Claude/Copilot agents |
| `javascript_standards.md` | ES6+ code style and formatting standards |
| `web_component_guide.md` | Standards for building web components |
| `testing-strategy.md` | Unit, integration, and E2E testing standards |
| `git_workflow.md` | Branching, commit, and PR requirements |
| `trace-element-spec.md` | Complete specification for the Trace Element component |
| `improved_dev_guidelines.md` | Index of all standards with links to real files |
| `README-STANDARD.md` | Template and rules for README.md files across all projects |
| `consolidation-log.md` | History of standards consolidation decisions |
| `reports/` | Audit and analysis outputs |

## Adding a New Project

1. Add an entry to `project-registry.json` with the project `name` and `path`
2. Create a `CLAUDE.md` in the project root pointing to this folder for global standards
3. The MCP server will automatically include it in cross-project scans

## Prerequisites

- `cielovista-tools` VS Code extension installed (provides the MCP server)
- Paths in `project-registry.json` must be valid local filesystem paths

## License

Copyright (c) 2026 CieloVista Software

---

## Purpose

_TODO: why does this standard exist? What problem does it prevent?_

---

## Rules

1. TODO: first rule — one clear sentence.
2. TODO: second rule.

---

## Changelog

- v1.0.0 (2026-05-08): Initial version
