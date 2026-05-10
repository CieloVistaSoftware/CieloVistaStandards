---
dewey: 000.4
id: web-component-guide
title: web component guide
project: global
description: Version: 3.0.0 Last Updated: 2026-04-02 Owner: Frontend Architecture Team Supersedes: v2.0.0 (class-inheritance / Shadow DOM pattern — do not use)
status: active
tags: [web, component, guide]
category: 800 — Global Standards
created: 2026-04-02
updated: 2026-04-27
version: 1.0.0
author: CieloVista Software
relativepath: web_component_guide.md
---
# Web Components Development Guide

**Version**: 3.0.0  
**Last Updated**: 2026-04-02  
**Owner**: Frontend Architecture Team  
**Supersedes**: v2.0.0 (class-inheritance / Shadow DOM pattern — do not use)

## Purpose

This guide defines how to create, register, and use behaviors in the WB Framework (wb-core). It replaces the v2.0 class-inheritance guide entirely. **Do not use the old BaseComponent pattern.**

---

## Core Philosophy

WB uses a **compositional behavior** model, not class inheritance.

- No `extends HTMLElement`. No Shadow DOM. No base classes.
- A **behavior** is a plain exported function: `function myBehavior(element, options) { ... return cleanup; }`
- Behaviors are injected onto any HTML element by the WB runtime—they do not own the element lifecycle
- Light DOM only—all output is rendered into the element itself, readable by the page and accessible to CSS
- The WB runtime (`core/wb.js`) handles scanning, observation, and cleanup

---

## Activation Patterns

There are four ways to activate a behavior. All are handled automatically by `WB.scan()` and `WB.observe()`.

### 1. Custom Tag — `<wb-name>`

Used for first-class components. The tag is mapped in `core/tag-map.js` `elementMap`.

```html
<wb-card title="Hello World">
  <p>Card content here.</p>
</wb-card>

<wb-tooltip text="Helpful hint">Hover me</wb-tooltip>
```

### 2. Behavior Attribute — `x-behavior="name"`

Applies a named behavior to any existing element.

```html
<pre x-behavior="pre" data-language="js" data-show-copy>
const x = 1;
</pre>

<div x-behavior="tooltip" data-tooltip="Hello">Hover me</div>
```

### 3. Shorthand Extension — `x-name`

Decorates an element with an effect or utility behavior.

```html
<button x-ripple>Click me</button>
<div x-tooltip="Helpful hint">Hover me</div>
<div x-draggable>Drag me</div>
<div x-sticky>Stays on scroll</div>
```

### 4. Morph — `x-as-name`

Transforms a plain element into a component layout without changing its tag.

```html
<div x-as-card title="Hello">Content</div>
<ul x-as-timeline>...</ul>
```

### Legacy — `data-wb` (OBSOLETE — DO NOT USE)

`data-wb="name"` is rejected at runtime with a console error and `x-error="legacy"` marker. It will never activate a behavior.

```html
<!-- WRONG — will not work, produces error -->
<pre data-wb="pre">...</pre>

<!-- CORRECT -->
<pre x-behavior="pre">...</pre>
```

---

## Behavior Function Signature

Every behavior is a plain exported function.

```javascript
/**
 * MyBehavior
 * Activation: x-behavior="mybehavior" or <wb-mybehavior>
 */
export function mybehavior(element, options = {}) {
  // Read config from options (programmatic) or data attributes (HTML)
  const label = options.label || element.dataset.label || '';
  const active = options.active ?? element.hasAttribute('data-active');

  // Mutate the Light DOM directly—no shadow, no innerHTML replacement
  element.classList.add('x-mybehavior');

  // Set up event listeners
  const onClick = () => { /* ... */ };
  element.addEventListener('click', onClick);

  // Return a cleanup function—called by WB.remove() or on disconnect
  return () => {
    element.removeEventListener('click', onClick);
    element.classList.remove('x-mybehavior');
  };
}

export default mybehavior;
```

**Rules:**
- Always return a cleanup function (even `() => {}` if nothing to clean)
- Read config from `options` first, fall back to `element.dataset.*`
- Never attach Shadow DOM
- Never use `class MyBehavior extends HTMLElement`
- Never use `customElements.define()` for behaviors (only for structural layout elements like `wb-grid`, `wb-stack`)

---

## Registering a New Behavior

### Step 1 — Create the behavior file

```text
wb-viewmodels/mybehavior.js
```

Export the function. The filename must match the behavior name exactly.

### Step 2 — Add to the behaviors index

```javascript
// wb-viewmodels/index.js
export { mybehavior } from './mybehavior.js';
```

### Step 3 — Register in tag-map.js (if using a custom tag)

```javascript
// core/tag-map.js — elementMap section
'wb-mybehavior': 'mybehavior',
```

If it is only activated via `x-behavior` or `x-mybehavior`, no tag-map entry is needed.

### Step 4 — Add a schema model (optional, for MVVM)

If the behavior uses a structured DOM template, add a JSON schema:

```text
src/wb-models/mybehavior.schema.json
```

---

## Bootstrap — Initializing the Framework

### Auto-init (recommended)

Drop the bootstrap script. It scans the DOM, injects behaviors, and starts the MutationObserver automatically.

```html
<script type="module" src="/core/wb-bootstrap.js"></script>
```

### With options

```html
<script type="module" src="/core/wb-bootstrap.js"
        data-debug
        data-auto-inject>
</script>
```

### Manual init

```javascript
import { bootstrap } from '/core/wb-bootstrap.js';

await bootstrap({
  debug: true,
  autoInject: false,   // auto-enhance native elements (input, pre, table, etc.)
  theme: 'dark',
  scan: true,          // scan DOM on init
  observe: true        // watch for new elements via MutationObserver
});
```

### Programmatic injection

```javascript
// Inject a single behavior onto an element
await WB.inject(element, 'tooltip', { tooltip: 'Hello', position: 'bottom' });

// Remove a behavior
WB.remove(element, 'tooltip');

// Scan a subtree (e.g., after dynamic content added)
await WB.scan(containerElement);
```

---

## Data Attribute Conventions

| Pattern | Purpose | Example |
|---|---|---|
| `data-language` | String config value | `data-language="js"` |
| `data-show-copy` | Boolean flag (presence = true) | `data-show-copy` |
| `data-max-height` | String with units | `data-max-height="300px"` |
| `data-size` | Enum value | `data-size="md"` |
| `x-behavior` | Explicit behavior activation | `x-behavior="pre"` |
| `x-ignore` | Exclude from auto-inject | `x-ignore` |
| `x-error` | Set by WB runtime on failure | (read-only) |

Behaviors should always read config with an `options` fallback chain:

```javascript
const value = options.myProp
  || element.dataset.myProp
  || defaultValue;
```

---

## New Behavior Checklist

- [ ] File is in `wb-viewmodels/` and named `mybehavior.js`
- [ ] Exported as a named function matching the file name
- [ ] Added to `wb-viewmodels/index.js`
- [ ] Added to `core/tag-map.js` if using a `<wb-*>` tag
- [ ] Returns a cleanup function
- [ ] Reads config from `options` first, then `element.dataset`
- [ ] No Shadow DOM, no class inheritance
- [ ] Adds a CSS class like `x-mybehavior` on activation
- [ ] CSS lives in `styles/` under its own file or component section
- [ ] Playwright test added in `tests/`
- [ ] Doc file added at `docs/components/[group]/mybehavior.md`

---

## Auto-Inject — Native Element Enhancement

When `autoInject: true` is set, WB automatically enhances native elements matching `nativeMap` in `core/tag-map.js`. This is opt-in (default off).

```javascript
// These are enhanced automatically when autoInject is enabled:
// <pre>          → pre behavior
// <code>         → code behavior  
// <table>        → table behavior
// <input type="checkbox">  → checkbox behavior
// etc.
```

To opt an element out of auto-injection:

```html
<pre x-ignore>raw output, no enhancement</pre>
```

---

## Layout Components (True Custom Elements)

A small set of structural layout components ARE registered as real custom elements using `customElements.define()`. These are not behaviors—they are structural containers:

| Tag | Purpose |
|---|---|
| `<wb-grid>` | CSS grid layout wrapper |
| `<wb-stack>` | Vertical stack layout |
| `<wb-row>` | Horizontal row layout |
| `<wb-column>` | Column layout |
| `<wb-cluster>` | Flexible cluster/wrap layout |
| `<wb-demo>` | Live code demo with markup preview |
| `<wb-search>` | Search input with behavior |

These use `class X extends HTMLElement` only because they need custom element lifecycle for structural reasons. All other components are behaviors.

---

## Testing

Tests use Playwright (not Jest, not Vitest).

```javascript
// tests/behaviors/pre.spec.ts
import { test, expect } from '@playwright/test';

test('pre behavior activates on x-behavior="pre"', async ({ page }) => {
  await page.goto('/tests/fixtures/pre.html');
  const wrapper = page.locator('.x-pre-wrapper');
  await expect(wrapper).toBeVisible();
});

test('pre behavior adds copy button when data-show-copy present', async ({ page }) => {
  await page.goto('/tests/fixtures/pre-copy.html');
  const btn = page.locator('.x-pre__copy');
  await expect(btn).toBeVisible();
});
```

Run tests:

```powershell
npm run test           # all tests
npm run test:ui        # Playwright UI mode
npm run test:headed    # headed browser
```

---

## Related Documents

- [JavaScript Standards](./javascript_standards.md)
- [Copilot Rules](./copilot-rules.md)
- [Git Workflow](./git_workflow.md)
- [WB Framework README](C:\dev\wb-core\README.md)
- [Tag Map](C:\dev\wb-core\core\tag-map.js) — all registered tags and behaviors

---

## Changelog

- **v3.0.0** (2026-04-02): Full rewrite for compositional behavior pattern. Replaces class-inheritance guide.
- **v2.0.0** (2025-09-13): Class-based BaseComponent pattern — superseded, do not use
- **v1.0.0** (2025-07-01): Initial guide