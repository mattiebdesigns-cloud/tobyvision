# Maias Agroforestry Slope Map — Project Roadmap

## Phase 1 — Proof of Concept (Complete)
Goal: Working interactive HTML map showing agroforestry-viable slopes across 9 Northeast states
(ME, NH, VT, MA, RI, CT, NY, NJ, PA)

- Leaflet.js base map with OpenStreetMap tiles
- USGS 3DEP slope layer via public WMS service
- Hillshade overlay from USGS National Map
- Visual highlighting of slope across full range
  (legend zones: <12% field crop, 12-20% agroforestry sweet spot, >20% manual labor terrain)
- Address / lat-lng search via Nominatim (no API key required)
- Parcel boundary toggle via Overpass API — default OFF, loads only at zoom 14+
- Standalone HTML file — no backend, no API keys required

### Known Phase 1 Limitations
- Slope layer uses USGS default color ramp, not filtered to 12-20% band
- Parcel coverage via OSM cadastral data — sparse in rural areas
- No click-to-inspect slope value at a point

---

## Phase 1.5 — Immediate Improvements
Goal: Polish the Phase 1 map before moving to precision data

- Click-to-inspect: show estimated slope % at any clicked point
- Save/bookmark candidate locations within a session (exportable as GeoJSON)
- Base layer switcher: OSM / Topo / Satellite imagery
- Better parcel fallback for areas with sparse OSM cadastral data

---

## Phase 2 — Pre-computed Slope Polygon Overlays (Path B)
Goal: Replace live WMS querying with pre-processed, locally-served slope zone polygons

### Architecture
- Download USGS 3DEP 10m DEM tiles for the 9-state Northeast region
- Compute slope raster (Python: rasterio + gdal)
- Classify into 3 bands: <12% (yellow), 12–20% (green), >20% (red)
  - In degrees: <6.84° / 6.84–11.31° / >11.31°
- Polygonize each band → GeoJSON / vector tiles
- Load in Leaflet as static overlay layers (no runtime API calls)

### Zoom-aware rendering
- Zoomed out  → simplified/generalized polygons for fast loading
- Medium zoom → moderately smoothed, more spatial detail
- Deep zoom   → full 10m resolution loads on demand
- Cloud Optimized GeoTIFF (COG) format for efficient tile serving

### Smart area targeting
- Do NOT process full 9-state coverage at high resolution
- Identify candidate zones from Phase 1 WMS layer
- Pull DEM tiles ONLY for those polygons + configurable buffer radius (default: 1–3 miles)
- Update polygons in place with refined data — no separate flagging layer

### Phase 2 Notes
- Buffer radius: configurable (default 1–3 miles around candidate zones)
- Smoothing: Gaussian blur on raster or polygon simplification (TBD)
- Hosting: local server → GitHub Pages → S3 bucket (TBD)
- Python stack: rasterio, gdal, geopandas

---

## Phase 3 — Enriched Layers
Goal: Add context layers that make the map useful for real land assessment

- Soil quality overlay (SSURGO data from USDA Web Soil Survey)
- Aspect layer — identify south-facing slopes (priority for most NE agroforestry)
- Watershed / water access proximity
- USDA Plant Hardiness Zone overlay
- Improved parcel data (evaluate Regrid free tier or state open data sources)
- Flood zone / wetland overlay (FEMA / NWI data)

---

## Phase 4 — Species & Suitability Intelligence
Goal: Cross-reference slope + soil + climate data with crop viability

- Species selector: given a location, show viable agroforestry crops
  (hazelnuts, chestnuts, pawpaw, elderberry, silvopasture species, etc.)
- Hazelnut-specific suitability score (slope + aspect + soil + hardiness zone)
- Site assessment summary: exportable PDF or shareable link for a candidate property
- Integration with PLANTS database (USDA) for species range data

---

## Phase 5 — Maias Ecosystem Integration
Goal: Embed as a standalone app within the Maias directional character framework

- Assign to appropriate directional character / energy
- Gamification layer: users assess and log candidate land parcels
- Connect to other Maias tools (species selection, design templates, permaculture patterns)
- Mobile-responsive layout for field use
- Offline mode for areas with poor connectivity

---

## Key Data Sources
- Slope / Elevation: USGS 3DEP (National Map WMS + bulk DEM download)
- Hillshade: USGS National Map ShadedReliefOnly tile service
- Parcel boundaries: OpenStreetMap Overpass API (Phase 1), Regrid (Phase 3 eval)
- Geocoding / Search: Nominatim (OpenStreetMap)
- Soils: USDA SSURGO / Web Soil Survey API
- Species range: USDA PLANTS database
- Climate zones: USDA Plant Hardiness Zone GIS data
- Flood / Wetlands: FEMA NFHL, USFWS National Wetlands Inventory

## Slope Thresholds (core design parameters)
- < 12%  : Annual field crop viable
- 12-20% : Agroforestry sweet spot — mechanized harvest viable (hazelnut, silvopasture, etc.)
- 20-25% : Transitional — adapted equipment only
- > 25%  : Manual labor terrain — agroforestry still possible, economics difficult

## Tech Stack
- Frontend: Leaflet.js, vanilla HTML/CSS/JS (no framework, no build step)
- Data processing (Phase 2+): Python (rasterio, gdal, geopandas)
- Tile serving (Phase 2+): Cloud Optimized GeoTIFF, TBD hosting
- No API keys required through Phase 1
