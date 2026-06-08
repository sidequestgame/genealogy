# Genealogy Map Tiles

Historical map tile sets (XYZ format) hosted on GitHub Pages for display as
**Tile Overlays in Google Earth Web**. Built from National Library of Scotland
(NLS) historical maps exported via QGIS.

Live site: https://sidequestgame.github.io/genealogy/

---

## Repository structure

Each map set lives in its own top-level folder, containing a standard XYZ tile
pyramid (`{z}/{x}/{y}.jpg`):

```
genealogy/
├── .nojekyll          # tells GitHub Pages to serve folders raw (do not delete)
├── README.md
└── Belfast/
    └── {z}/{x}/{y}.jpg   # zoom levels 12–17
```

To add a new area (e.g. Ballymena), create a sibling folder: `Ballymena/{z}/{x}/{y}.jpg`.

### Tile URL pattern

```
https://sidequestgame.github.io/genealogy/<MapSet>/${z}/${x}/${y}.jpg
```

For Belfast:

```
https://sidequestgame.github.io/genealogy/Belfast/${z}/${x}/${y}.jpg
```

---

## Displaying a map set in Google Earth Web

1. Open https://earth.google.com/web and sign in.
2. **New Project** (or open an existing one) in the left panel.
3. **Add → Tile Overlay** (menu bar), or the **⋯** overflow menu above the
   feature list → **Tile Overlay**.
4. Fill in the inspector card:
   - **Overlay URL pattern:** the URL above for your map set
   - **Tile size:** `256`
   - **Tile coverage** (Belfast): N `54.6188`, S `54.5197`, E `-5.8052`, W `-6.0127`
5. Click **Done**. It saves to your Google account.

The project **syncs to your Google account**, so it appears automatically on any
machine where you sign in to Google Earth Web — no file to carry between computers.

> **Note on coverage box & rounding:** Google Earth Web only displays one decimal
> place (e.g. `54.6188` shows as `54.6`). This does **not** matter. The coverage
> box does not position or align the imagery — tiles self-align by their
> `{z}/{x}/{y}` coordinates. The box only hints at where to request tiles; a loose
> box just means a few harmless 404s for tiles outside the area.

---

## Adding a new map set (QGIS → GitHub)

### 1. Export tiles from QGIS

- Set project CRS to **EPSG:3857** (Web Mercator).
- Processing Toolbox → **Generate XYZ tiles (Directory)**.
- Key settings:
  - **Tile format:** JPG  *(smaller than PNG; fine for opaque historical maps)*
  - **Tile size:** 256 × 256
  - **Zoom:** min 12, max 17 *(adjust to taste; higher max = many more tiles)*
  - **TMS-convention: OFF**  ← important. Off = standard XYZ (Google/OSM) tiling,
    which is what Google Earth Web expects. On would flip the Y axis.
  - **Extent:** the bounding area of your map.
- Output to a local folder.

Record the extent — convert the EPSG:3857 bounds to lat/lon for the GE Web
coverage box (or just use an approximate bounding box; see rounding note above).

### 2. Add to this repo

Place the exported `{z}` folders **inside a named subfolder** (not at the repo
root), e.g. `Ballymena/12/…`, then commit and push:

```bash
git add Ballymena
git commit -m "Add Ballymena map tiles"
git push
```

Large pushes can fail with an HTTP RPC error. If so, raise the buffer once:

```bash
git config http.postBuffer 524288000
```

GitHub Pages redeploys automatically (~1–2 min). Verify a tile in a browser:

```
https://sidequestgame.github.io/genealogy/Ballymena/12/<x>/<y>.jpg
```

---

## Why this approach (and what does NOT work in Google Earth Web)

These were learned the hard way:

- **KMZ super-overlays / NetworkLinks / region-based overlays** work in Google
  Earth **Desktop (Pro)** but **not** in Google Earth **Web**. This is why
  desktop overlays never carried over to the browser.
- **GroundOverlay of a single huge image** works in web but loses resolution when
  zoomed in.
- **Native Tile Overlay** (this method) is the reliable way to get high-res tiled
  rasters into Google Earth **Web**.
- **CORS matters:** Google Earth Web fetches tiles cross-origin, so the host must
  send `Access-Control-Allow-Origin: *`. GitHub Pages does this automatically —
  which is why this works. (A custom server that omits the CORS header would fail
  silently in the web version even if the URL loads fine in a browser.)
- The `.nojekyll` file disables Jekyll processing so all folders are served as-is.

---

## Map sets

| Area      | Folder       | Zooms | Tiles | Source |
|-----------|--------------|-------|-------|--------|
| Belfast   | `Belfast/`   | 12–17 | 5,934 | NLS historical maps |
| Ballymena | `Ballymena/` | 12–17 | 8,933 | NLS historical maps |
