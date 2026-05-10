---
dewey: 150.4
id: doc-contract-standard
title: CieloVista Doc Contract Standard
project: cielovista-tools
description: Defines the required front-matter contract for all .md docs in registered CieloVista projects
status: active
owner: CieloVista Software
lastUpdated: 2026-05-06
tags: [standards, docs, front-matter, dewey]
relativePath: exemplar-conforming-doc.md
---

# CieloVista Doc Contract Standard

This document is an exemplar of a fully conforming CieloVista doc.
It demonstrates all required and optional front-matter fields.

## What the contract requires

Every `.md` doc in a registered project needs six front-matter fields:
`subject`, `id`, `title`, `project`, `description`, and `status`.

The `subject` field combines the project's hundred-prefix with a subject sub-code
from the standardized taxonomy (e.g. `150.4` = cielovista-tools / Policy & Standards).

The `id` field is a stable lowercase-kebab-case slug that never changes after assignment.

## Why stable identity matters

Docs move. Folders get renamed. File paths are unreliable as long-term references.
The `{subject}.{id}` pair (e.g. `150.4.doc-contract-standard`) is permanent —
it survives any reorganization and remains valid in bookmarks, cross-references, and tool output.

## Validator

Run `validate_doc` via the MCP tools or `cvs.docs.validate` command to check any doc
against this contract. Run `list_doc_violations` to scan all 19 registered projects at once.
