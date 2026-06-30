# Astryx v0.1.2 — CDN Test

Testing Astryx v0.1.2's CDN support with React 19 and StyleX — **no bundler required**.

## What This Tests

- **Astryx 0.1.2 ESM build** from esm.sh (`?external=react,react-dom` for shared React instance)
- **React 19.2.7** from esm.sh (React 19 dropped UMD builds, so we use ESM import maps)
- **StyleX runtime** — bundled inside the Astryx ESM build, no separate import needed
- **Astryx CSS** (reset + pre-compiled StyleX + all theme CSS) from unpkg
- **4 themes** (neutral, stone, matcha, butter) with runtime switching — JS from esm.sh, CSS from unpkg
- **Theme fonts** from Google Fonts (Figtree, Montserrat, DM Sans, Playwrite US Trad, Outfit, JetBrains Mono)

## Architecture

```
┌─ index.html ───────────────────────────────────────────────────────┐
│                                                                      │
│  <link> Google Fonts  ← fonts.googleapis.com (all theme fonts)     │
│  <link> reset.css     ← unpkg @astryxdesign/core                    │
│  <link> astryx.css    ← unpkg @astryxdesign/core (pre-compiled)    │
│  <link> theme.css ×4  ← unpkg @astryxdesign/theme-{neutral,stone,…}│
│                                                                      │
│  <importmap>                                                       │
│    react           → esm.sh/react@19.2.7                           │
│    react/jsx-runtime → esm.sh/react@19.2.7/jsx-runtime             │
│    react-dom       → esm.sh/react-dom@19.2.7                       │
│    react-dom/client → esm.sh/react-dom@19.2.7/client               │
│    htm             → esm.sh/htm@3.1.1                             │
│                                                                      │
│  <script type="module">                                            │
│    1. import React, { createRoot }, htm from esm.sh               │
│    2. import Astryx from esm.sh (?external=react,react-dom)       │
│    3. import 4 theme objects from esm.sh/@astryxdesign/theme-*    │
│    4. render app with htm (JSX without a bundler)                 │
│                                                                      │
└──────────────────────────────────────────────────────────────────────┘
```

## Why ESM instead of UMD?

v0.1.2 ships a UMD bundle (`dist/astryx.umd.js`, global `Astryx`), but the ESM approach via esm.sh is cleaner:

- **No global pollution** — `window.React` / `window.Astryx` hacks not needed
- **Single React instance** — `?external=react,react-dom` makes esm.sh emit bare `react` imports that resolve through the import map, guaranteeing one shared React
- **Native module loading** — `import()` instead of dynamic `<script>` tag injection
- **Tree-shakeable** — esm.sh serves only the modules you actually import

The UMD bundle still works if you prefer it (see [v0.1.2 release notes](https://github.com/facebook/astryx/releases/tag/v0.1.2)).

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
| Astryx ESM | `https://esm.sh/@astryxdesign/core@0.1.2?external=react,react-dom` |
| Astryx UMD (alt) | `https://unpkg.com/@astryxdesign/core@0.1.2/dist/astryx.umd.js` |
| Astryx CSS | `https://unpkg.com/@astryxdesign/core@0.1.2/dist/astryx.css` |
| Reset CSS | `https://unpkg.com/@astryxdesign/core@0.1.2/src/reset.css` |
| Theme CSS | `https://unpkg.com/@astryxdesign/theme-{name}@0.1.2/dist/theme.css` |
| Theme JS | `https://esm.sh/@astryxdesign/theme-{name}@0.1.2/built` |
| React 19 | `https://esm.sh/react@19.2.7` |
| React DOM | `https://esm.sh/react-dom@19.2.7/client` |
| Theme Fonts | `https://fonts.googleapis.com/css2?family=Figtree&family=Montserrat&…` |

**Available themes:** neutral, stone, matcha, y2k, chocolate, butter, gothic

## Running Locally

```bash
# Any static file server works
npx serve .
# or
python3 -m http.server 8000
```

## Deploy

Pushes to `main` automatically deploy to GitHub Pages via GitHub Actions.

## Acknowledgments

- [@imdreamrunner (Xinzi Zhou)](https://github.com/imdreamrunner) — ESM approach via esm.sh with `?external=react,react-dom` (PR #1)

## License

MIT
