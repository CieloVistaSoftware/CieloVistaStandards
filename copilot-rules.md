---
dewey: 000.4
id: cielovista-ai-assistant-rules
title: CieloVista AI Assistant Rules
project: global
description: - CVT = Cielo Vista tools project/folder - CVT path: C:\Users\jwpmi\Downloads\VSCode\projects\cielovista-tools
status: active
tags: [copilot, rules, cielovista]
category: 800 — Global Standards
created: 2026-03-14
updated: 2026-04-27
version: 1.0.0
author: CieloVista Software
relativepath: copilot-rules.md
---
# CieloVista AI Assistant Rules

> **Global standard — applies to ALL CieloVista projects and all AI assistants.**
> This includes Copilot, Claude, ChatGPT, Gemini, and any future assistant.
> Single source of truth. Do not duplicate this file into individual projects.
> Location: `C:\Users\jwpmi\Downloads\CieloVistaStandards\copilot-rules.md`

## Terminology

- CVT = Cielo Vista tools project/folder
- CVT path: `C:\Users\jwpmi\Downloads\VSCode\projects\cielovista-tools`

## Command Runtime Policy

- This project is PowerShell-only for command execution.
- All command examples and automation must use PowerShell syntax.
- `.sh`/bash scripts are not supported and must not be suggested.

## Code Suggestion Rules

1. Always include the full file path in every suggestion.
2. Format suggestions as JSON objects with line numbers and full file path.
3. Be friendly, concise, and focus on readability and maintainability.
4. Never show shell (sh) commands — use PowerShell or TypeScript.
5. Always include type annotations in TypeScript.
6. Point out potential bugs and anti-patterns.
7. Suggest more descriptive names for variables and functions.
8. Recommend breaking down long functions into smaller, reusable ones.
9. Encourage try-catch blocks for better error handling.
10. Point out duplicated code and suggest utility functions.

## Language Priorities

- JavaScript, TypeScript, PowerShell, Python, .NET/C#
- ES modules only — never CommonJS (no require, no module.exports)
- Strict TypeScript — always use type annotations

## Formatting

```json
{
  "line": 1,
  "suggestion": "Suggestion text here.",
  "filePath": "C:/full/path/to/file.ts"
}
```

## What NOT To Do

- Never show sh/bash commands — always PowerShell on Windows
- Never create or suggest `.sh` files for project workflows
- Never suggest var — always const or let
- Never leave error handling empty (no silent catch blocks)
- Never duplicate logic that already exists in shared utilities

## Cross-Assistant Compatibility

Use these rules as policy text in each tool's custom instructions area.

- Copilot: VS Code custom instructions or workspace instruction files
- Claude: project instructions / Claude.md style policy docs
- ChatGPT: project instructions or persistent custom instructions
- Gemini: project-level instruction/context settings

If a tool cannot enforce JSON suggestion formatting natively, require the model to emit
equivalent structured output with `line`, `suggestion`, and `filePath` fields.
