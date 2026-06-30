# Astryx v0.1.2 — CDN Test

Testing Astryx v0.1.2's new UMD CDN bundle with React 19 and StyleX — **no bundler required**.

## What This Tests

- **Astryx 0.1.2 UMD bundle** (`dist/astryx.umd.js`, global `Astryx`) from unpkg
- **React 19.2.7** from esm.sh (React 19 dropped UMD builds, so we use ESM import maps)
- **StyleX runtime** — bundled inside the Astryx UMD, no separate import needed
- **Astryx CSS** (reset + pre-compiled StyleX + theme) from unpkg
- **Neutral theme** from esm.sh

## Architecture

```
┌─ index.html ────────────────────────────────────────────────┐
│                                                              │
│  <link> reset.css        ← unpkg @astryxdesign/core          │
│  <link> astryx.css      ← unpkg @astryxdesign/core          │
│  <link> theme.css       ← unpkg @astryxdesign/theme-neutral │
│                                                              │
│  <importmap>                                               │
│    react → esm.sh/react@19.2.7                              │
│    react-dom/client → esm.sh/react-dom@19.2.7               │
│    htm → esm.sh/htm@3.1.1                                  │
│                                                              │
│  <script type="module">                                    │
│    1. import React from esm.sh                              │
│    2. set window.React / window.ReactDOM globals            │
│    3. <script src="astryx.umd.js"> ← expects global React    │
│    4. import { neutralTheme } from esm.sh                   │
│    5. render app with htm (JSX without a bundler)           │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

## Why htm instead of Babel/JSX?

No build step. `htm` provides tagged template literals that compile to `React.createElement` calls at runtime. It's ~1KB and lets us write JSX-like syntax in a single HTML file with no bundler.

## What's New in v0.1.2

- **UMD bundle** — `dist/astryx.umd.js` exposes global `Astryx` with 240 exports
- **`unpkg`/`jsdelivr` fields** in package.json for CDN auto-resolution
- **StyleX runtime bundled in** — no separate `@stylexjs/stylex` import needed
- **Button `isInterruptible`** — keep clickable during async actions
- **`useTableStickyColumns`** — pin table columns with scroll-aware shadows
- **Breaking**: `color="active"` → `color="accent"` on Text, Heading, Link, Timestamp

## CDN URLs

| Resource | URL |
|----------|-----|
| Astryx UMD | `https://unpkg.com/@astryxdesign/core@0.1.2/dist/astryx.umd.js` |
| Astryx CSS | `https://unpkg.com/@astryxdesign/core@0.1.2/dist/astryx.css` |
| Reset CSS | `https://unpkg.com/@astryxdesign/core@0.1.2/src/reset.css` |
| Theme CSS | `https://unpkg.com/@astryxdesign/theme-neutral@0.1.2/dist/theme.css` |
| React 19 | `https://esm.sh/react@19.2.7` |
| React DOM | `https://esm.sh/react-dom@19.2.7/client` |
| Neutral Theme | `https://esm.sh/@astryxdesign/theme-neutral@0.1.2/built` |

## Running Locally

```bash
# Any static file server works
npx serve .
# or
python3 -m http.server 8000
```

## Deploy

Pushes to `main` automatically deploy to GitHub Pages via GitHub Actions.

## License

MIT
