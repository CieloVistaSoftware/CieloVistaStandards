---
dewey: 000.4
id: cielovista-subject-taxonomy
title: CieloVista Subject Taxonomy
project: global
description: Standardized second-level subject codes, applied identically to every registered project. The X in each code is replaced by the project's assigned hundred (e.g. 150 for cielovista-tools).
status: draft
relativePath: subject-taxonomy.md
---
# CieloVista Subject Taxonomy

Standardized second-level subject codes, applied identically to every registered project.

The `X` in each code is replaced by the project's assigned hundred (e.g. `150` for cielovista-tools).

| Sub-code | Category | Description |
|---|---|---|
| `X.1` | Components / Features | Individual features, UI components, commands, or modules |
| `X.2` | Architecture | System design, data flow, integration patterns, ADRs |
| `X.3` | Testing | Test strategy, test plans, known failures, coverage policy |
| `X.4` | Policy & Standards | Coding standards, review rules, contribution guidelines |
| `X.5` | AI Coordination | Prompts, agent instructions, CLAUDE.md guidance, AI tooling |
| `X.6` | Release & Deployment | Release notes, deployment steps, versioning, changelogs |
| `X.7` | Getting Started | Onboarding, setup, quickstart, prerequisites |
| `X.8` | API / Reference | API docs, type reference, schema definitions, CLI reference |
| `X.9` | Meta | Roadmap, status docs, planning, session notes, parking lots |

## Extension

Projects needing categories beyond `X.9` may use `X.10`, `X.11`, etc.
These must be documented in the project's own CLAUDE.md or README.

## Skipping sub-codes

A project without (for example) a public API simply has no docs at `X.8`.
Sub-codes are not required to be contiguous.
