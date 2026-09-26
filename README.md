# sketch-diagrams

Hand-drawn architecture diagrams for [Astro](https://astro.build), rendered to inline SVG at build time.

- Strokes by [rough.js](https://roughjs.com), the engine behind Excalidraw's look
- Labels in [Excalifont](https://github.com/excalidraw/excalidraw/tree/master/packages/excalidraw/fonts/Excalifont), Excalidraw's handwriting face (bundled, SIL OFL)
- Icons from [Lucide](https://lucide.dev) (`'key-round'`) and brand marks from [Simple Icons](https://simpleicons.org) (`'si:kubernetes'`)
- Automatic grid layout: boxes size themselves to their text, column gaps grow to fit edge labels
- Deterministic: the same `seed` renders the same strokes on every build
- No image files, no client-side JavaScript, themeable with CSS variables

Live demo: **https://sketch-diagrams.pages.dev** · Built for the figures on [bitof.dev](https://bitof.dev).

## Install

```bash
npm install github:juanzete/sketch-diagrams
```

```astro
---
import Diagram from 'sketch-diagrams';
import 'sketch-diagrams/styles.css';   // theme (CSS variables)
import 'sketch-diagrams/fonts.css';    // Excalifont @font-face
---
<Diagram
  title="Keyless trust between two clouds"
  caption="Identity federation in both directions."
  nodes={[
    { id: 'ci',  label: 'Build runner',    sub: 'cloud A · builds images', col: 0, row: 0, kind: 'accent', icon: 'hammer' },
    { id: 'sts', label: 'Token exchange',  sub: 'cloud B · trusts A',      col: 1, row: 0, icon: 'key-round' },
    { id: 'reg', label: 'Registry B',      sub: 'cloud B · images',        col: 2, row: 0, kind: 'warm', icon: 'package' },
    { id: 'k8s', label: 'Partner cluster', sub: 'cloud B · pulls images',  col: 2, row: 1, kind: 'warm', icon: 'si:kubernetes' },
  ]}
  edges={[
    { from: 'ci', to: 'sts', label: 'signed OIDC token' },
    { from: 'sts', to: 'reg', label: 'credentials · 1h' },
    { from: 'k8s', to: 'reg', label: 'pull', dashed: true },
  ]}
/>
```

Vite (and therefore Astro) resolves the relative font URLs in `fonts.css` and bundles the woff2 files.
If your setup does not, copy `node_modules/sketch-diagrams/fonts/excalifont` into `public/fonts/` and point
`@font-face` at it.

## Props

| Prop | Type | Notes |
| --- | --- | --- |
| `title` | string | Accessible title of the figure (`<title>` in the SVG) |
| `caption` | string | Rendered below as `figcaption` |
| `nodes` | Node[] | See below |
| `edges` | Edge[] | `{ from, to, label?, dashed?, both? }` |
| `seed` | number | Stroke randomness seed, default 42 |
| `roughness` | number | rough.js roughness, default 1.0 |
| `colGap`, `rowGap` | number | Minimum gaps in viewBox units (90 / 70) |
| `width`, `height` | number | Override the computed viewBox |

Node: `{ id, label, sub?, icon?, kind?, col?, row?, x?, y?, w?, h? }`.
`kind` is `default`, `accent` (hatched), `warm` (hatched) or `muted` (dashed). `col`/`row` place the node on the
grid; `x`/`y`/`w`/`h` override the layout for that node.

## Theming

Everything is a CSS variable on `:root`, override on any ancestor:

```css
.light {
  --sd-bg: #fbf8f1; --sd-border: #ddd6c6; --sd-text: #1c1b18; --sd-sub: #8b877d;
  --sd-default: #5f5c55; --sd-accent: #1f6f43; --sd-warm: #b4452a; --sd-muted: #a8a397;
  --sd-line: #8b877d; --sd-label: #5f5c55; --sd-caption: #8b877d;
}
```

## Development

```bash
npm install
npm --prefix demo install
npm run demo          # http://localhost:4321
npm run build:demo    # demo/dist
```

## License

MIT. Excalifont is © Excalidraw under the SIL Open Font License 1.1 (see `fonts/excalifont/LICENSE.txt`).
Icon data is read from the `lucide` (ISC) and `simple-icons` (CC0) packages at build time.
