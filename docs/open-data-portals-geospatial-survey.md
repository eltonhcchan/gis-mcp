# Open data portals — geospatial services survey

Comparative scan of national geospatial open-data portals: services offered, GIS stacks, key datasets, activity tracking, and API-key policy. Prepared **22 Nov 2026 (HKT)** as background for GIS MCP / CSDI-style integrations.

Interactive companion: [Open Data Portals geospatial survey canvas](https://cursor.com/canvas/store-5768011d-415a-40ff-a1ce-2d14415bd9a4/6d076478-2b4f-4482-9ca2-0001dd04b782).

For which sites already expose **MCP** (vs OGC / REST only), see [Geospatial MCP landscape](geospatial-mcp-landscape.md).

## Headline

National portals mostly front **OGC WMS / WFS** (and increasingly **OGC API Features / Tiles**) and/or **ArcGIS REST** behind a catalogue. API keys fall into three camps: absent (fair-use), optional (higher quotas), or mandatory (metering). Aggregate usage tracking is common; personal activity feeds are not.

## Portals surveyed (12)

| Portal | Stack | Services | Key datasets | API key | Activity tracking |
|--------|-------|----------|--------------|---------|-------------------|
| HK CSDI | Esri Geoportal + ArcGIS + OGC | WFS, WMS, REST, CSW, Map / 3D APIs | 1,100+ themes | Partial (map / 3D keyed) | Public KPIs; visit logging |
| Singapore OneMap / data.gov.sg | Custom REST | Geocode, search, tiles, datastore | Address, planning, transport | Yes / optional | Rate tiers |
| Korea VWorld | National + OGC | Search, geocoder, WMS / WFS / WMTS, 3D | Cadastre, zoning, 3D | Yes | Per-key quotas |
| NZ LINZ | Koordinates + GeoServer | WFS, WMTS, changeset | Parcels, topo, hydro, imagery | Yes (required) | Account keys |
| Australia data.gov.au / NationalMap / ELVIS | CKAN + TerriaJS + agency GIS | WMS / WFS / REST; ELVIS order | Boundaries, DEM / LiDAR | Usually no | CKAN stats |
| NL PDOK | OGC-first | WMS / WMTS / WFS / WCS, OGC API | BAG, BGT, BRT, cadastre | No | Fair-use ops |
| CH geo.admin.ch | GeoAdmin REST + STAC | Search, identify, WMTS, STAC | Names, cadastre, hazards | No | 429 / 403 fair-use |
| FR IGN Géoplateforme | Géoplateforme + data.gouv | WFS 2.0, WMTS | BD TOPO, Admin Express | No (IP throttle) | Rate limits |
| UK OS / data.gov.uk | OS APIs + CKAN | Features, tiles, NGD | MasterMap, NGD, OpenData | Yes | Per-project metering |
| EU data.europa.eu | DCAT-AP hub | Search / SPARQL → national GIS | Federated INSPIRE / HVD | No (read) | MQA metrics |
| US Data.gov / GeoPlatform | api.data.gov + OGC GeoAPI + Hub | Catalog + OGC collections | TIGER, USGS, NOAA… | Catalog key; GeoAPI open | api.data.gov tracking |
| Canada Open Maps / FGP | CKAN + OGC + ArcGIS | WMS / WFS / REST tiles | CanVec, NHN, HRDEM | No | Publishes GA4 stats |

## How they offer geospatial services

1. **Catalogue + linked services** — CKAN / data.gov / data.europa.eu publish metadata and point to WMS / WFS / ArcGIS URLs. The catalogue is not the GIS engine.
2. **National geoportal APIs** — PDOK, geo.admin.ch, IGN, VWorld, OneMap, LINZ expose first-class map / feature / tile APIs.
3. **Map viewers / hubs** — NationalMap, Open Maps, ArcGIS Hub, CSDI portal map for visual discovery.
4. **Order / download systems** — ELVIS, PDOK Atom / download APIs for bulk DEM / LiDAR.

## Recurring dataset themes

| Theme | Typical offerings | Common access |
|-------|-------------------|---------------|
| Admin & cadastre | Boundaries, parcels, addresses, place names | WFS / FeatureServer / GeoJSON |
| Transport & hydro | Roads, rail, water networks | WMS / WFS, vector tiles |
| Elevation & imagery | DEM / DSM, LiDAR, orthoimagery, bathymetry | WCS, WMTS, STAC, order portals |
| Buildings & land use | Building footprints, zoning, planning | WFS / REST / 3D tiles |
| Base maps | Official topographic / transport basemaps | WMTS, XYZ, Vector Tiles |

## API-key policy mix

| Camp | Portals | What the key buys |
|------|---------|-------------------|
| Mandatory | LINZ, Ordnance Survey, VWorld, OneMap | Account attribution, quotas, sometimes billing |
| Partial / optional | CSDI map / 3D APIs, data.gov.sg, US Catalog API | Anonymous at low limits; key raises throughput |
| No key (fair-use) | PDOK, Switzerland, IGN, Canada maps, typical AU OGC | IP / User-Agent limits; hard 429 / 403 when abused |

## Live verification (22 Nov 2026)

- CSDI homepage: 1,100+ datasets; 2.05M+ API / download metrics for 2025
- `api3.geo.admin.ch` MapServer: HTTP 200, CORS open, no key
- IGN `data.geopf.fr/wfs`: rate-limit headers (`ratelimit-limit: 30`)
- VWorld: Kong `www-authenticate: Key realm="kong"` without key

## Patterns useful for GIS MCP

1. Catalogue → typed distributions → WFS / FeatureServer / GeoJSON
2. Prefer no-key or optional-key portals for MCP extras
3. Treat LINZ / OS / VWorld / OneMap as credentialed integrations
4. CSDI dataset APIs already match the open-OGC camp; specialty Map / 3D APIs match the keyed camp
