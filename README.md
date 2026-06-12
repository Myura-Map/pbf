# Myura PGF glyph ranges

Static hosting for **positioned glyph font (PGF)** `.pbf` files used with [MapLibre GL JS](https://maplibre.org) and the [maplibre-gl-complex-text](https://github.com/wipfli/maplibre-gl-complex-text) plugin.

Built from [Myura-Map/Myura_UFOS `tools/font_encoder`](https://github.com/Myura-Map/Myura_UFOS/tree/main/tools/font_encoder).

**Live site:** [myura-map.github.io/pbf](https://myura-map.github.io/pbf)

## Available fonts

| Script | Font | Version | Ranges |
|--------|------|---------|--------|
| Khmer (`khm`) | Hanuman Regular | v1 | `63488-63743` |

### Direct URLs

```
https://myura-map.github.io/pbf/khm/Hanuman-Regular-v1/63488-63743.pbf
```

### MapLibre glyph URL template

```
https://myura-map.github.io/pbf/khm/Hanuman-Regular-v1/{range}.pbf
```

Replace `{range}` with the 256-codepoint block MapLibre requests (e.g. `63488-63743`).

## Usage with MapLibre

Khmer labels need the **complex-text plugin** (HarfBuzz shaping) plus these pre-generated SDF glyphs. See [khmer-text-rendering.md](https://github.com/Myura-Map/Myura_UFOS/blob/main/samples/khmer-text-rendering.md) for background.

### `transformRequest` (redirect PUA ranges)

Use when your map style already has a `glyphs` URL and you only want to override shaped-glyph ranges:

```javascript
maplibregl.setRTLTextPlugin(
    'https://wipfli.github.io/maplibre-gl-complex-text/dist/maplibre-gl-complex-text.js',
    false
);

const hanumanRangeStarts = [63488];

map.setTransformRequest((url, resourceType) => {
    if (resourceType !== 'Glyphs') return undefined;

    const match = url.match(/(\d+)-(\d+)\.pbf$/);
    if (!match) return undefined;

    const start = parseInt(match[1], 10);
    if (!hanumanRangeStarts.includes(start)) return undefined;

    const end = match[2];
    return {
        url: `https://myura-map.github.io/pbf/khm/Hanuman-Regular-v1/${start}-${end}.pbf`
    };
});
```

### Style `glyphs` property

Alternatively, point the style root `glyphs` at this CDN (font stack name must match how MapLibre requests ranges):

```json
{
  "glyphs": "https://myura-map.github.io/pbf/khm/Hanuman-Regular-v1/{fontstack}/{range}.pbf"
}
```

Note: PGF setups often use `transformRequest` instead, because the font stack in requests may not match the folder name.

## GitHub Pages

1. Push this repo to `main`.
2. In GitHub → **Settings → Pages**, set source to **Deploy from branch** → `main` → `/ (root)`.
3. Files are served from `https://myura-map.github.io/pbf/`.

`.nojekyll` is included so Jekyll does not skip binary `.pbf` files.

## Adding a new font

1. Run the [font_encoder](https://github.com/Myura-Map/Myura_UFOS/tree/main/tools/font_encoder) pipeline (`encoding.py`, then `build_pbf.sh`).
2. Copy output into a script folder, e.g. `khm/Siemreap-Regular-v1/`.
3. Update this README and `index.html`.
4. Push to `main`.

## License

- **Hanuman** — [SIL Open Font License](https://openfontlicense.org/) (Google Fonts).
- Glyph range data derived from [wipfli/word-corpus](https://github.com/wipfli/word-corpus) (OSM ODbL, Wikipedia CC BY-SA, Wikidata CC0).
