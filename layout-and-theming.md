# CieloVista Layout and Theming Standard

Rules every CieloVista web page follows, whatever project it is in. They came
from wb-starter's `docs/standards/DEMOS-AND-DOCS-STANDARDS.md` and now live
here so that every site, not only wb-starter, can find them. wb-starter's
standard links here for these rules.

---

## 1. Spacing: at least 1rem

- **Sides:** content never touches the viewport edge. Every page has at least
  **1rem** of padding on the left and right, at every width.
- **Between blocks:** sections, cards and examples have at least **1rem** of
  vertical space between them.
- **Inside boxes:** cards, panels and demo containers have at least **1rem**
  of padding.
- Spacing comes from the theme's spacing variables or a layout behavior's own
  default (`x-container` gives `padding: 1rem` and `gap: 1rem`), never from
  one-off values written on the page.

## 2. Colours: from the theme only

- Every colour comes from a theme CSS variable (`--bg-primary`,
  `--text-primary`, `--primary`, `--primary-text`, `--secondary`,
  `--border-color`, ...). Only the theme file may hold colour literals.
- A translucent shade is a mix of a theme variable, not a fixed `rgba()`:
  `color-mix(in srgb, var(--bg-primary) 70%, transparent)`.
- **Check it:** switch between a dark and a light theme. Every part of the page
  must change. A part that stays the same colour has a hard-coded colour.

## 3. Phone and desktop: measure both

- A layout is checked at a phone width (**375px**) and at desktop width before
  it is called done. A fix checked only at desktop is half checked.
- At 375px there is **no sideways scroll**: nothing is wider than the viewport.
- Wide-screen ideas (side-by-side panels, fixed heights, inner scroll areas)
  go inside a breakpoint, and the narrow layout keeps ordinary page flow.

## 4. Media fits its box

- An `<img>`, `<video>`, `<iframe>` or `<svg>` is never wider than the box it
  sits in, and is never stretched past its natural size. That takes both
  `max-width: 100%` and no forced `width: 100%`.

## 5. Popups stay on screen

- A popover, tooltip, dropdown or menu never spills outside the viewport or its
  parent. It flips, moves or clamps to stay fully visible.

## 6. Layout comes from layout behaviors

In wb-starter-based sites, page layout uses wb-starter's layout behaviors, not
page-specific CSS:

| Behavior | Use it for |
|---|---|
| `x-container` | A centred, width-capped column with 1rem padding and gap |
| `x-grid` | A responsive grid of cards (`columns="3"`) |
| `x-flex` | A row that wraps |
| `x-stack` | A vertical stack |
| `x-cluster` | A wrapping group of small items (tags, buttons) |
| `x-center` | A width-capped block centred with side gutters |
| `x-sidebarlayout` | A sidebar beside main content |

---

## Related Documents

- [Web Component Guide](./web_component_guide.md)
- [JavaScript Standards](./javascript_standards.md)
- [wb-starter Demos and Docs Standards](https://github.com/CieloVistaSoftware/wb-starter/blob/main/docs/standards/DEMOS-AND-DOCS-STANDARDS.md): the rules that only apply to wb-starter's own demos and docs

---

id: cielovista-layout-and-theming
title: CieloVista Layout and Theming Standard
project: global
description: Spacing (1rem sides and gaps), theme-only colours, phone and desktop checks, media and popup bounds, and layout behaviors for every CieloVista web page.

type: Standard
tags: [layout, theming, spacing, css, responsive]
category: 800 — Global Standards
created: 2026-10-20
updated: 2026-10-20
version: 1.0.0
author: CieloVista Software
relativepath: layout-and-theming.md
---
