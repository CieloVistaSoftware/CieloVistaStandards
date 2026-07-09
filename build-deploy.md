# Build & Deploy — Caching Rules

## Why this doc exists

2026-07-09: wb-starter shipped an installable PWA (`manifest.json` with
`display: standalone`) with **no service worker**. An installed/home-screen
instance had zero mechanism to detect new deploys, so it held stale HTML/CSS/JS
indefinitely — refreshing did nothing, because nothing was checking the
network. Root-caused and fixed in commit `9f0aaa6`. This doc exists so the
next project doesn't reintroduce the same gap, and so "it still looks broken"
reports get diagnosed correctly instead of assumed to be a code bug.

## Rule 1 — Any manifest with `display: standalone`/`fullscreen`/`minimal-ui` MUST ship a network-first service worker

If a project's `manifest.json` makes it installable, a service worker is not
optional — without one, an installed instance has no update-checking
mechanism at all and relies purely on the OS webview's own cache heuristics,
which can hold stale assets far longer than a browser tab and can't be
inspected or controlled from your side.

The service worker MUST be **network-first**, not cache-first:

```js
self.addEventListener('fetch', event => {
  if (event.request.method !== 'GET') return;
  event.respondWith(
    fetch(event.request).then(response => {
      if (response.ok) {
        const clone = response.clone();
        caches.open(CACHE_VERSION).then(cache => cache.put(event.request, clone));
      }
      return response;
    }).catch(() => caches.match(event.request))   // offline fallback only
  );
});
```

Cache-first (`caches.match(req).then(cached => cached || fetch(req))`) is
banned for the main fetch handler — it makes the cache authoritative over the
network, which is exactly backwards. The cache exists only as an offline
fallback, never as the primary source. A refresh must always prefer live
network content when the network is reachable.

## Rule 2 — The service worker file MUST live at the site root

A service worker's default max scope is the directory it's served from.
GitHub Pages (and most static hosts) cannot send a custom
`Service-Worker-Allowed` header to widen that scope from a subdirectory
(e.g. `/src/sw.js`) to the whole site. Put `sw.js` at the project root and
register it with `{ scope: './' }` (or equivalent) so it controls every page,
not just the directory it lives in.

## Rule 3 — Bump the cache name on meaningful releases

```js
const CACHE_VERSION = 'wb-cache-v2';   // bump this string when it matters
```

The `activate` handler must delete any cache key that isn't the current
`CACHE_VERSION`, so a version bump automatically evicts stale caches from any
previously-installed worker — including ones that predate this standard and
were cache-first.

## Rule 4 — Verifying a deploy is live: don't trust one browser tab

GitHub Pages sits behind a Fastly CDN with `Cache-Control: max-age=600`,
which is normal and fine — it is NOT usually the source of "still looks
broken" reports. Before concluding a deploy didn't take effect:

1. `curl` the deployed URL directly (bypasses your browser's cache and any
   installed service worker entirely) and grep for the expected change.
2. If curl shows the fix but a browser doesn't, check in a **private/incognito
   window** — this rules out your regular profile's disk cache and any
   installed PWA/home-screen instance in one step.
3. Only after both of those still show stale content should you suspect a
   real deployment or CDN propagation problem — check `X-Cache`/`Age`
   response headers across a few requests to confirm.

Projects without a manifest/PWA (plain static GH Pages sites) are much lower
risk here — a normal refresh after the 10-minute CDN window will pick up
changes on its own. This whole class of bug is specific to installable PWAs
that have (or are missing) a service worker.

## Sweep log

- 2026-07-09: audited all `type: website`/web-facing projects in
  `project-registry.json` for `manifest.json` + missing/cache-first service
  worker. Only wb-starter had a manifest; fixed. JesusFamilyTree has no
  manifest — not exposed to this issue.

---
type: Standard
---
