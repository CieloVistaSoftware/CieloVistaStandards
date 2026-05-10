---
docid: 000.4.issue-filing-rules
dewey: 000.4
id: issue-filing-rules
title: Issue Filing Rules — CieloVista Software
project: global
description: Standard rules for filing, reproducing, fixing, and closing GitHub issues across all CieloVista projects.
status: active
tags: [issues, testing, workflow, standards]
category: 000.4 — Policy & Standards
created: 2026-05-09
updated: 2026-05-09
version: 1.0.0
author: CieloVista Software
relativepath: issue-filing-rules.md
---

# Issue Filing Rules — CieloVista Software

> Every issue is a contract: reproduce it with a test, fix it, prove it.

---

## Purpose

These rules apply to every GitHub issue filed against a CieloVista project. They exist to ensure issues are reproducible, fixes are verified, and the historical record explains not just *what* was done but *why* it worked.

---

## Rule 1 — What Goes in the Issue (Opening)

Every new issue must include all of these:

### 1.1 Component Area

Tag the issue with the component it belongs to (see [Component Areas](#component-areas)). This determines which component test suite must pass before closing.

### 1.2 Stack Trace or Error Log

Include the full stack trace or relevant error log entries. This is the primary signal for locating the broken code path. If no exception was thrown, include:
- The sequence of user actions that produced the wrong result
- Any console errors or CVT output-channel entries
- Network requests that failed (URL, status code, response body)

### 1.3 Current Behavior

Describe exactly what happens. Be specific — "it fails" is not enough. Include:
- What was being done (e.g. "searching for `.json` in `C:\Users\jwpmi\Downloads`")
- Current settings/inputs at the time of failure
- What the UI showed or what was returned

### 1.4 Expected Behavior

One sentence: what should have happened.

### 1.5 Repro Steps

Numbered, copy-pasteable steps that reliably reproduce the failure from a clean state.

---

## Rule 2 — First Step After Filing: Write a Failing Test

Before writing any fix, write a test that **reproduces the problem** and **currently fails**.

- The test must live in the component's test area (see [Component Areas](#component-areas))
- The test name must reference the issue number: `REG-NNN` or `#NNN`
- Run the test to confirm it fails — do not proceed until this is verified
- Commit the failing test as a standalone commit so the regression is recorded

This is non-negotiable. A fix without a prior failing test is not verifiable.

---

## Rule 3 — Fix and Retest

1. Implement the fix
2. Run the failing test — it must now pass
3. Run **all tests for the affected component** — none may regress
4. Run the **full regression suite** (`node scripts/run-regression-tests.js`) — all must pass
5. If any unrelated tests break, fix them before closing

---

## Rule 4 — Component Areas

Every issue belongs to one component area. Each area has a designated test location and a label used on GitHub issues.

| Component Area | GitHub Label | Test Location |
|---|---|---|
| Doc Catalog | `scope:doc-catalog` | `tests/unit/doc-catalog*.test.js` |
| Doc Intelligence | `scope:doc-intelligence` | `tests/unit/doc-intelligence.test.js` |
| README Compliance | `scope:readme-compliance` | `tests/unit/readme-compliance.test.js`, `tests/regression/REG-02x–03x` |
| Broken Refs | `scope:broken-refs` | `tests/unit/docs-broken-refs.test.js`, `REG-032` |
| MCP Viewer | `scope:mcp-viewer` | `tests/regression/REG-035` |
| CVT Command Launcher | `scope:launcher` | `tests/unit/cvs-command-launcher.test.js`, `tests/regression/REG-017` |
| NPM Output | `scope:npm-output` | `tests/regression/REG-025`, `REG-026` |
| Daily Audit | `scope:daily-audit` | `tests/unit/daily-audit*.test.js`, `REG-024` |
| Doc Catalog (MCP) | `scope:mcp-catalog` | `tests/unit/mcp-*.test.js` |
| Error Log | `scope:error-log` | `tests/unit/error-log*.test.js` |
| Home Page | `scope:home` | `tests/home-*.test.js` |
| Extension Core | `scope:extension` | `tests/regression/REG-001–REG-015` |
| DiskCleanUp | `scope:diskcleanup` | `tests/` in DiskCleanUp project |

To run all tests for a component area: `node tests/unit/<component>.test.js` or filter by label in the regression runner.

---

## Rule 5 — Closing Requirements

An issue **must not be closed** until:

- [ ] The originally failing test now passes
- [ ] All component tests for the affected area pass
- [ ] The full regression suite passes (REG-001 through the highest REG-NNN)
- [ ] The closing comment is written (see Rule 6)

If any of the above cannot be met, the issue stays open with a comment explaining what is blocking it.

---

## Rule 6 — Closing Comment (In-Depth Technical Detail)

The closing comment is the permanent record. It must contain:

### 6.1 Root Cause

Explain *why* the bug existed. Reference exact file paths and line numbers. This is the most important part — a fix description without a root cause is incomplete.

Example:
> **Root cause:** In `selectTab()` (`src/features/mcp-viewer/html.ts`, line 939), `runEndpoint(null)` was called unconditionally after `loadProjectOptions()` had already called `runEndpoint({ projectName: "wb-core" })`. Both requests raced; the unfiltered request (larger payload) consistently completed last and overwrote the filtered result.

### 6.2 Fix Description

Describe what was changed, why it works, and include the before/after code if short enough to fit.

### 6.3 Test Added

Name the test file, the number of checks, and what each check verifies.

### 6.4 Commits

List the commit SHA(s) that implement the fix.

---

## Rule 7 — No Silent Fixes

If a fix is discovered to have accidentally broken something else (a regression-within-a-regression), that must be called out explicitly in the issue or a new linked issue, never silently patched without documentation.

---

## Changelog

| Date | Change |
|---|---|
| 2026-05-09 | Initial version — drafted from session rules discussion |
