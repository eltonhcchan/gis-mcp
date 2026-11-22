# Geospatial MCP landscape

Survey of Model Context Protocol servers for geospatial and open-data access, and the tool patterns they share. Compiled **22 Nov 2026 (HKT)** as background for GIS MCP integrations (including CSDI-style catalogue tools).

Interactive companion: [Geospatial MCP Landscape canvas](https://cursor.com/canvas/store-5768011d-415a-40ff-a1ce-2d14415bd9a4/34960817-9978-47db-9539-a658c99be7d6).

Sparkgeo’s curated catalog tracks **117** geospatial MCP servers (health check 15 Nov 2026).

!!! note "Important split"
    Almost no classic government open-data portals (`data.gov`, PDOK, CSDI, `data.gouv.fr`) ship an official MCP endpoint yet. What exists today is: (1) a few national or open-geo sites with hosted MCP, (2) commercial map and platform vendors, (3) aggregators that wrap many open datasets, and (4) community or local servers that speak OGC, STAC, OSM, ArcGIS, or GeoServer.

## Who already offers geospatial MCP?

### Open-data / government-adjacent (rare)

| Name | Endpoint / form | What you get | Key? |
|------|-----------------|--------------|------|
| [bharatlas](https://bharatlas.com/mcp) (India open geo) | Hosted MCP + npm | Admin boundaries → villages, wards, rivers, forests, infra; list / query / locate / nearby | No (read) |
| Swisstopo SGS LLM exploration MCP | Public CloudFront `/mcp` prototype | Swiss catalogue discovery, admin divisions, geocode, GeoAdmin preview, parcel / ÖREB point explore | Public read-only prototype |
| NASA Earthdata MCP | Official NASA tooling | CMR discovery, verify, access Earth science datasets | Earthdata login as needed |
| dynamical.org Weather & Climate | Local + hosted | Open STAC weather / climate archives (GFS, HRRR, ECMWF / AIFS) | Open |
| Registry of Open Data on AWS (RODA) | Local MCP (awslabs) | Search 1,000+ public S3 datasets incl. satellite / climate; STAC discovery | Open |

Classic portals (CSDI, PDOK, data.gov.uk, geo.admin.ch REST, IGN Géoplateforme, LINZ, and similar) still expose OGC / REST — not first-party MCP. Agents reach them via wrappers (`mcp-ogc`, `geoserver-mcp`, TerraLab, GIS MCP gatherers).

See also: [Open data portals geospatial survey](open-data-portals-geospatial-survey.md).

### Aggregators

| Name | Hosted URL | Coverage | Notes |
|------|------------|----------|-------|
| [TerraLab MCP](https://mcp.terra-lab.ai/mcp) | `https://mcp.terra-lab.ai/mcp` | Worldwide OSM / Overture + DEM + Sentinel; deeper France (cadastre, PLU, IGN) | ~24–40 tools; OAuth / API key; licence-aware extracts |
| [LGND Geo](https://geo.lgnd.ai/mcp) | `https://geo.lgnd.ai/mcp` | Sentinel-2 global + US NAIP embeddings | Search / explore / classify / verify from imagery |
| GeoLens MCP | Self-hosted against a GeoLens instance | Your published catalog | `search_datasets`, schema, `get_features`, read-only SQL; map authoring with key |

### Commercial platforms with official MCP

| Vendor | Typical tools | Access |
|--------|---------------|--------|
| Mapbox, TomTom, HERE, Stadia, MapQuest, Geoapify | Geocode, places, routing, matrix, isochrones, static maps | API key / account |
| Esri ArcGIS Location Services MCP | Geocoding, routing, static maps (Location Platform) | Esri account |
| CARTO, Felt, GIS Cloud, Geobble | Explore warehouse data, style maps, spatial SQL / workflows, publish URLs | Platform login |
| Planet, SkyWatch, SkyFi, Vexcel, Microsoft PC Pro | Search / preview / order satellite or aerial imagery | Commercial (often paywalled) |
| Regrid, LandRecords.us, ATTOM, Pluia | Parcels, ownership, zoning, French PLU | Commercial / freemium |

### Analysis / infrastructure MCPs

These are not open-data portals, but they are how agents actually do GIS:

| Server | Role | Example tools |
|--------|------|---------------|
| **GIS MCP** (this project) | Geoprocessing toolkit + data gatherers | 92+ tools: Shapely, GeoPandas, Rasterio, PyProj, PySAL, climate / ecology / land-cover / satellite download |
| [geoserver-mcp](related-mcp-servers.md) / mcp-geo-server | Drive a GeoServer instance | Workspaces, layers, CQL, WMS map, WFS query; optional `/geoserver/mcp` extension |
| mcp-ogc | Any OGC WMS / WMTS / WFS | `list_wms_layers`, `get_wms_map`, `list_wmts_layers`, `get_wmts_tile`, `query_wfs_features` |
| arcgis-online-mcp | ArcGIS Online / Enterprise portal | `search_content`, `describe_layer`, `query_features`, geocode; optional writes |
| OSM / Overpass / PlaceRoot / Overture MCP | Open map features | Geocode, Overpass GeoJSON, buildings, places, isochrones |
| stac-mcp / planetary-computer-mcp / copernicus-mcp | Earth observation catalogs | Search STAC, download Sentinel / Landsat scenes |
| QGIS / ArcGIS Pro MCP bridges | Desktop GIS automation | Layer management, processing algorithms, styling, layouts |

## Typical tools in a geospatial MCP

Across portals, aggregators, and analysis servers, toolkits cluster into these families. A full geospatial MCP stack usually combines several.

| Family | Typical tools | Role |
|--------|---------------|------|
| Discover | `search_datasets`, `list_layers`, `find_datasets`, `search_content` | Keyword, bbox, category; returns id / title / bbox — not full geometry |
| Describe | `get_layer_schema`, `describe_layer`, `get_dataset_schema` | Columns, geometry type, CRS, extent, feature count; schema-before-query |
| Query / extract | `query_layer`, `get_features`, `query_wfs_features`, `query_features` | Bbox, attribute filter, CQL / SQL, paging, GeoJSON or file download |
| Locate & nearby | geocode, reverse_geocode, locate (point-in-polygon), nearby, `find_places` | Names → coordinates and nearby features |
| Maps & tiles | `get_wms_map`, `get_wmts_tile`, static map, create web map | Rendered images or interactive map URLs |
| Terrain & routes | `get_elevation`, `get_route`, isochrone, travel times, matrix | DEM, profiles, contours, network travel |
| Imagery & rasters | STAC search, satellite download, NDVI / index, zonal statistics | EO search and raster summary |
| Geoprocess & analyze | buffer, clip, overlay, spatial join, CRS transform, spatial stats, visualize | GIS MCP core |

### Canonical agent workflow

| Step | Typical tools | Why |
|------|---------------|-----|
| 1. Discover | search / `list_layers` | Find the right dataset id |
| 2. Describe | `get_schema` / describe | Know fields, CRS, size before pull |
| 3. Extract | query / download / WFS GetFeature | Bounded GeoJSON or file on disk |
| 4. Analyze | buffer, sjoin, zonal, stats | Answer with real geospatial ops |
| 5. Present | static / interactive map, file path | Human-checkable output |

## Fit for GIS MCP

Catalogue-style integrations (search → describe → download / query features) match the **Discover → Describe → Extract** pattern used by Bharatlas, GeoLens, mcp-ogc, and ArcGIS Online MCP. Existing GIS MCP Shapely / GeoPandas / Rasterio tools then cover **Analyze → Present**.

That is the mainstream geospatial MCP architecture — not one tool per dataset. Pair a **data MCP** (portal wrapper or aggregator) with an **analysis MCP** (GIS MCP) for end-to-end agent workflows.

## Sources

- [Sparkgeo geo-mcp-servers](https://github.com/sparkgeo/geo-mcp-servers) (117 servers)
- [TerraLab MCP](https://terra-lab.ai/mcp), [LGND Geo](https://lgnd.ai/resources/lgnd-geo), [Bharatlas MCP](https://bharatlas.com/mcp)
- [Swisstopo sgs-llm](https://github.com/swisstopo/sgs-llm), GeoLens docs, mcp-ogc, GIS MCP README
- Related page: [Related MCP Servers](related-mcp-servers.md)
