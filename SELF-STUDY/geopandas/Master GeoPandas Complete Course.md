# Master GeoPandas: Complete Course

Nov 22, 2026 · @Thisara Priyamal

This 8-week course takes you from loading your first shapefile to running spatial joins, overlays and publishable maps, covering the whole GeoPandas toolkit with a use case for each topic.

## Course overview

**Who it is for:** analysts, data scientists, urban planners, environmental scientists and GIS users who know basic Python and pandas. GeoPandas extends pandas with geometry columns so you can analyse maps, boundaries, routes and locations in a DataFrame workflow.

**What you will be able to do:** read and write any common vector format, manage coordinate systems correctly, measure and transform geometries, join datasets by location, aggregate by region, draw static and interactive maps, and scale spatial analysis efficiently.

**Prerequisites:** Python, pandas basics (the companion pandas course covers it) and Matplotlib basics. No GIS background is assumed.

| Week | Modules | Milestone |
| --- | --- | --- |
| 1 | 1 to 2 | Understand vector data and CRS; build GeoDataFrames |
| 2 | 3 to 4 | Read and write formats; reproject correctly |
| 3 | 5 to 6 | Measure geometries; buffer, clip, overlay, union |
| 4 | 7 to 8 | Spatial joins and regional aggregation |
| 5 | 9 | Choropleths and interactive maps |
| 6 | 10 | Connect rasters, OSM, geocoders, PostGIS |
| 7 | 11 | Optimise performance on large layers |
| 8 | 12 | Finish two capstone projects |

```python
import geopandas as gpd
import pandas as pd
import matplotlib.pyplot as plt
from shapely.geometry import Point, LineString, Polygon

print(gpd.__version__)
```

## Module 1: Setup and geospatial foundations

Learn the vocabulary of spatial data and the libraries GeoPandas stands on.

| Topic | Description | Key tools | Use case |
| --- | --- | --- | --- |
| Installation | Prefer conda-forge (handles GDAL, GEOS, PROJ binaries); pip wheels also work on most platforms | `conda install -c conda-forge geopandas`, `pip install geopandas` | Reproducible environments |
| The stack | GeoPandas combines pandas (tables), Shapely (geometry), pyogrio or Fiona (file I/O), pyproj (coordinate systems), plus optional Matplotlib, folium, mapclassify, contextily | `shapely`, `pyproj`, `pyogrio` | Know which library does what |
| Vector vs raster | Vector data stores discrete features (points, lines, polygons); raster data stores grids of cell values. GeoPandas handles vector; use rasterio for raster | Concept | Choose the right tool |
| Geometry types | Point, LineString, LinearRing, Polygon, MultiPoint, MultiLineString, MultiPolygon, GeometryCollection | Shapely classes | Model real-world features |
| Coordinates | Longitude and latitude in degrees, or x and y in metres/feet in a projected system; order matters (x = lon, y = lat) | Concept | Avoiding swapped coordinates |
| Coordinate reference system (CRS) | Defines how coordinates map to the Earth; identified by EPSG codes such as 4326 (WGS 84 lat/lon) and 3857 (web mercator) | `gdf.crs` | Correct distances and areas |
| Attributes plus geometry | Each row is a feature: ordinary columns plus one active geometry column | `GeoDataFrame` | pandas workflows on maps |
| Sample data | The old bundled datasets were removed in GeoPandas 1.0; use the `geodatasets` package or Natural Earth downloads | `geodatasets.get_path('nybb')` | Practice material |
| Common data sources | Natural Earth, OpenStreetMap, national statistics offices, open data portals, Overture Maps | Download then `read_file` | Real projects |
| Typical workflow | Load, check CRS, clean, reproject, analyse, map, export | Concept | Project template |

```python
import geodatasets

nyc = gpd.read_file(geodatasets.get_path('nybb'))
nyc.head()
nyc.crs                    # coordinate reference system
nyc.geometry.geom_type     # Polygon / MultiPolygon
nyc.total_bounds           # [minx, miny, maxx, maxy]

nyc.plot(column='BoroName', legend=True, figsize=(6, 6))
```

**Exercises**

1. Install the stack in a clean environment and print the versions of GeoPandas, Shapely, pyproj and the I/O engine.
2. Load a boundary dataset and report its CRS, number of features, geometry types and bounds.
3. Explain in your own words why measuring area in EPSG:4326 gives meaningless square degrees.

## Module 2: GeoSeries, GeoDataFrame and creating geometries

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Shapely geometries | Build single shapes | `Point(x, y)`, `LineString([...])`, `Polygon([...])`, `MultiPoint`, `box(minx, miny, maxx, maxy)` | Hand-made features |
| `GeoSeries` | A pandas Series of geometries with spatial methods | `gpd.GeoSeries([...], crs='EPSG:4326')`, `GeoSeries.from_wkt`, `from_xy`, `from_wkb` | Geometry-only work |
| `GeoDataFrame` | A DataFrame with a geometry column | `gpd.GeoDataFrame(df, geometry=, crs=)` | Attributes plus shapes |
| Points from coordinates | Turn lat/lon columns into points | `gpd.points_from_xy(df.lon, df.lat, crs='EPSG:4326')` | CSVs of stores, sensors, events |
| From WKT or WKB text | Parse geometry from strings | `gpd.GeoSeries.from_wkt(df['wkt'])`, `shapely.wkt.loads` | Database exports |
| From GeoJSON-like data | Features and dictionaries | `gpd.GeoDataFrame.from_features(features, crs=)` | API responses |
| Active geometry | Choose or rename the geometry column | `set_geometry('col')`, `rename_geometry`, `gdf.geometry`, `gdf.geometry.name` | Several geometry columns |
| Setting the CRS | Declare the existing CRS (does not change coordinates) | `crs='EPSG:4326'`, `set_crs` | Correct labelling |
| pandas operations still work | Filtering, merging, groupby, sorting keep geometry | `gdf[gdf['pop'] > 1e6]`, `gdf.merge(...)` | Familiar workflow |
| Converting back | Drop geometry for plain tables | `pd.DataFrame(gdf.drop(columns='geometry'))`, `gdf.geometry.to_wkt()`, `to_json()` | Exports to non-spatial tools |
| Coordinate access | Pull x and y from points | `gdf.geometry.x`, `.y`, `.get_coordinates()` | Plain numeric columns |
| Indexing by location | Select by bounding box | `gdf.cx[xmin:xmax, ymin:ymax]` | Quick rectangular subset |
| Empty and missing geometry | Handle gaps | `is_empty`, `isna`, `dropna(subset=['geometry'])` | Clean data |
| Geometry columns beyond the first | Store centroids, buffers alongside originals | `gdf['centroid'] = gdf.centroid` then `set_geometry` | Multi-geometry tables |

```python
cities = pd.DataFrame({
    'city': ['Colombo', 'Kandy', 'Galle'],
    'lon': [79.8612, 80.6337, 80.2170],
    'lat': [6.9271, 7.2906, 6.0535],
    'pop': [752993, 125400, 99478],
})

gdf = gpd.GeoDataFrame(
    cities,
    geometry=gpd.points_from_xy(cities['lon'], cities['lat']),
    crs='EPSG:4326',
)

line = gpd.GeoSeries([LineString([(79.86, 6.93), (80.63, 7.29)])], crs=4326)
zone = gpd.GeoSeries([box(79.5, 6.5, 80.5, 7.5)], crs=4326)

gdf.cx[79.5:80.5, 6.5:7.5]          # bounding-box selection
gdf.geometry.x                       # longitudes
```

**Exercises**

1. Convert a CSV of latitude and longitude columns to a GeoDataFrame and verify the CRS.
2. Build a polygon for a delivery zone and test which of your points fall inside it.
3. Parse a column of WKT strings into geometries and find invalid or empty ones.

## Module 3: Reading and writing spatial data

| Format | Read / write | Notes | Use case |
| --- | --- | --- | --- |
| Any OGR format | `gpd.read_file(path)`, `gdf.to_file(path, driver=)` | Engine is `pyogrio` by default in GeoPandas 1.x (older versions used Fiona) | One function for most formats |
| Shapefile (.shp) | `read_file('a.shp')`, `to_file('a.shp')` | Several sidecar files; 10-character column names; 2 GB limit | Legacy GIS data |
| GeoJSON | `read_file('a.geojson')`, `to_file('a.geojson', driver='GeoJSON')`, `to_json()` | Text, usually EPSG:4326 | Web maps and APIs |
| GeoPackage (.gpkg) | `to_file('a.gpkg', layer='roads', driver='GPKG')` | Single file, many layers, modern default | Recommended exchange format |
| Layers | List and select | `gpd.list_layers('a.gpkg')`, `read_file(path, layer='roads')` | Multi-layer files |
| GeoParquet / Feather | `gpd.read_parquet`, `to_parquet`, `read_feather`, `to_feather` | Fast, compact, columnar | Large analytical datasets |
| KML, GPX, FlatGeobuf, others | Through OGR drivers | `driver='KML'`, `'GPX'`, `'FlatGeobuf'` | GPS tracks, Google Earth |
| Zip files and URLs | Read remote or compressed data | `read_file('zip://data.zip')`, `read_file('https://.../a.geojson')` | Open data downloads |
| Read less | Limit rows, columns or area while reading | `read_file(path, bbox=, mask=, rows=, columns=, where=)`, `ignore_geometry=True` | Large files |
| CSV with geometry | Parse WKT or lon/lat | `pd.read_csv` then `GeoSeries.from_wkt` or `points_from_xy` | Tabular sources |
| Databases | PostGIS and SpatiaLite | `gpd.read_postgis(sql, con, geom_col='geom')`, `gdf.to_postgis(name, engine, if_exists=)` | Production data stores |
| Encoding | Fix text issues | `encoding='utf-8'`, `'latin1'` | Non-ASCII names |
| Write options | Choose driver, layer, mode | `mode='a'` to append, `index=False`, `engine=` | Incremental saves |
| Round trip checks | Verify CRS, row count, dtypes after saving | `crs`, `len`, `dtypes` | Data integrity |

```python
roads = gpd.read_file('city.gpkg', layer='roads')

# only the area and columns you need
subset = gpd.read_file('big.gpkg', bbox=(79.8, 6.8, 80.0, 7.0),
                       columns=['name', 'type'])

roads.to_file('out.gpkg', layer='roads_clean', driver='GPKG')
roads.to_parquet('roads.parquet')          # fast reload
roads.to_file('roads.geojson', driver='GeoJSON')

print(gpd.list_layers('out.gpkg'))
```

Note: shapefile column names are cut to 10 characters, and GeoJSON generally expects EPSG:4326, so reproject before exporting to the web.

**Exercises**

1. Convert one shapefile to GeoPackage and GeoParquet and compare file sizes and read times.
2. Read only the features inside a bounding box from a large file.
3. Store three layers in one GeoPackage and list them back.
4. Save a GeoDataFrame to GeoJSON and open it in a web map viewer.

## Module 4: Coordinate reference systems and projections

The most common source of wrong GIS results is a missing or mismatched CRS. Master this module first.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Geographic vs projected CRS | Geographic uses angles (degrees); projected uses a flat plane in metres or feet. Distance and area need a projected CRS | `gdf.crs.is_geographic`, `is_projected` | Choose the right system |
| EPSG codes | Standard identifiers | `4326` (WGS 84), `3857` (web mercator), `326xx` / `327xx` (UTM zones), national grids such as `5235` or `5234` for Sri Lanka grids | Naming systems |
| Inspect | View details | `gdf.crs`, `gdf.crs.to_epsg()`, `gdf.crs.axis_info`, `crs.area_of_use`, `crs.to_wkt()` | Debugging |
| `set_crs` | Declare what coordinates already mean (no coordinate change) | `gdf.set_crs(4326)`, `allow_override=True` | Files with no CRS |
| `to_crs` | Transform coordinates to another CRS | `gdf.to_crs(3857)`, `to_crs(epsg=32644)`, `to_crs(other.crs)` | Reprojection |
| Estimating a good CRS | Pick a local UTM zone from the data | `gdf.estimate_utm_crs()` | Accurate local measurement |
| Matching CRS before combining | Joins and overlays require equal CRS | `a.crs == b.crs`, `b = b.to_crs(a.crs)` | Avoiding silent errors |
| Equal-area projections | Preserve area | Albers, Mollweide, `ESRI:54009`, `EPSG:6933` | Area statistics, density |
| Distance-preserving choices | Local azimuthal or UTM | `+proj=aeqd` custom CRS | Radius searches |
| Custom CRS | PROJ strings, `pyproj.CRS` | `pyproj.CRS.from_proj4`, `from_user_input` | Special projections |
| Axis order | Lat/lon vs lon/lat confusion | `always_xy=True` in `pyproj.Transformer` | Manual transformations |
| Pandas-free transform | Transform raw coordinates | `pyproj.Transformer.from_crs(4326, 3857, always_xy=True).transform(x, y)` | Arrays of numbers |
| Antimeridian and polar cases | Geometries crossing 180 degrees or the poles need care | Split or shift longitudes | Global datasets |
| Web mapping | Web tiles use EPSG:3857; store and measure in another CRS | `to_crs(3857)` only for display | Basemaps |

```python
cities = gpd.GeoDataFrame(
    {'city': ['Colombo', 'Kandy']},
    geometry=gpd.points_from_xy([79.8612, 80.6337], [6.9271, 7.2906]),
    crs='EPSG:4326',
)

cities.crs.is_geographic              # True
utm = cities.estimate_utm_crs()       # e.g. EPSG:32644
cities_m = cities.to_crs(utm)

# distance in metres between the two points
d = cities_m.geometry.iloc[0].distance(cities_m.geometry.iloc[1])
print(round(d / 1000, 1), 'km')

# never combine layers with different CRS
parks = parks.to_crs(cities.crs)
assert parks.crs == cities.crs
```

Remember: `set_crs` labels, `to_crs` converts. Using `set_crs` to "fix" wrongly placed data hides the problem.

**Exercises**

1. Load a layer with no CRS, determine the correct one from its coordinate values, and set it.
2. Reproject a country boundary to a local projected CRS and compare its area to a published value.
3. Compare distances between two cities in EPSG:4326 (degrees), EPSG:3857 and a UTM zone; explain the differences.
4. Use `estimate_utm_crs()` on three datasets in different regions.

## Module 5: Geometry attributes, measurements and validity

All of these are vectorised: call them on a whole GeoSeries or GeoDataFrame at once.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Area and length | In CRS units, so use a projected CRS | `gdf.area`, `gdf.length`, `gdf.geometry.length` | Parcel size, road length |
| Perimeter / boundary | Edges of polygons | `gdf.boundary`, `exterior` | Outlines |
| Bounds | Extent per feature or whole layer | `gdf.bounds`, `gdf.total_bounds`, `envelope`, `minimum_bounding_circle()`, `minimum_rotated_rectangle()` | Extents, zooming |
| Centres | Points representing a shape | `centroid`, `representative_point()` (always inside), `geometry.centroid` | Labels, nearest-point analysis |
| Distance | Between layers or to one geometry | `gdf.distance(other)`, `hausdorff_distance`, `frechet_distance` | Nearest facility, similarity |
| Along lines | Measure and sample on lines | `line.project(point)`, `line.interpolate(d)`, `normalized=True` | Mile markers, sampling |
| Coordinates | Extract vertices | `get_coordinates()`, `count_coordinates()`, `count_geometries()`, `x`, `y`, `z` | Vertex statistics |
| Geometry type | Check kinds | `geom_type`, `has_z`, `is_ring`, `is_closed`, `is_simple` | Data checks |
| Validity | Detect broken geometry (self-intersections, bad rings) | `is_valid`, `is_valid_reason()`, `make_valid()`, `buffer(0)` | Before overlay or union |
| Emptiness | Find blank or null shapes | `is_empty`, `isna()` | Cleaning |
| Compactness metrics | Shape descriptors | `4 * pi * area / perimeter**2` | Gerrymandering, urban form |
| Densities and ratios | Combine attributes with area | `gdf['pop'] / (gdf.area / 1e6)` | People per square kilometre |
| Spherical alternatives | Geodesic length and area without reprojecting | `gdf.to_crs(...).area`, or `pyproj.Geod` | Global-scale measurement |

```python
nyc = gpd.read_file(geodatasets.get_path('nybb')).to_crs(2263)   # feet

nyc['area_km2'] = nyc.area * 0.092903 / 1e6     # sq ft to sq m to sq km
nyc['perimeter_km'] = nyc.length * 0.3048 / 1000
nyc['centroid'] = nyc.centroid

bad = nyc[~nyc.is_valid]
nyc['geometry'] = nyc.make_valid()

from math import pi
nyc['compactness'] = 4 * pi * nyc.area / nyc.length ** 2

pts = gpd.GeoSeries([Point(0, 0), Point(3, 4)], crs=3857)
pts.iloc[0].distance(pts.iloc[1])     # 5.0
```

**Exercises**

1. Compute the area in square kilometres and the population density for each district.
2. Find every invalid polygon in a layer, explain why it is invalid, and repair it.
3. Calculate how far each school is from the nearest hospital.
4. Place a label point inside each irregular polygon using `representative_point`.

## Module 6: Geometry operations

Create new geometries from existing ones: buffers, simplifications, combinations and transformations.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Buffer | Zone around a feature at a given distance (use a projected CRS) | `gdf.buffer(500)`, `cap_style=`, `join_style=`, `resolution=`, negative buffers shrink | Service areas, setbacks |
| Simplify | Fewer vertices, similar shape | `gdf.simplify(tolerance, preserve_topology=True)` | Lighter maps, faster processing |
| Convex and concave hulls | Wrap a set of points | `convex_hull`, `concave_hull(ratio=)` | Habitat or service extent |
| Envelope and bounding shapes | Rectangles and circles | `envelope`, `minimum_rotated_rectangle()`, `minimum_bounding_circle()` | Extents |
| Union | Merge many geometries into one | `gdf.union_all()` (older name `unary_union`), `a.union(b)` | Combine parcels |
| Intersection, difference, symmetric difference | Element-wise set operations | `a.intersection(b)`, `a.difference(b)`, `a.symmetric_difference(b)` | Overlap and remainder shapes |
| `clip` | Cut a layer to a boundary | `gpd.clip(gdf, mask)` | Subset roads to a city |
| `overlay` | Full layer-on-layer operations | `gpd.overlay(a, b, how='intersection'/'union'/'difference'/'symmetric_difference'/'identity')` | Land-use analysis, areas of overlap |
| Explode multi-part | Split into single parts | `gdf.explode(ignore_index=True)` | Islands as separate rows |
| Dissolve | Merge by attribute (see Module 8) | `gdf.dissolve(by=)` | Region boundaries |
| Voronoi and Delaunay | Proximity polygons and triangulation | `gdf.voronoi_polygons()`, `delaunay_triangles()` (GeoPandas 1.x) | Catchment areas |
| Affine transforms | Move, rotate, scale, skew | `translate`, `rotate`, `scale`, `skew`, `affine_transform` | Aligning layouts |
| Line operations | Combine and split lines | `shapely.ops.linemerge`, `substring`, `offset_curve()` | Road network work |
| Snapping and noding | Fix small gaps | `shapely.ops.snap`, `set_precision(grid_size)` | Clean topology |
| Polygon to points / lines | Change geometry kind | `boundary`, `exterior`, `get_coordinates`, `sample_points(n)` | Random sampling inside polygons |
| Segmentize and densify | Add vertices along edges | `segmentize(max_segment_length)` | Accurate reprojection of long lines |
| Reverse and orientation | Fix ring direction | `reverse()`, `shapely.geometry.polygon.orient` | Standards compliance |

```python
stops = stops.to_crs(32644)                 # projected, metres

catchment = stops.buffer(400)               # 400 m walking zone
coverage = catchment.union_all()            # one combined shape

city_roads = gpd.clip(roads.to_crs(32644), city.to_crs(32644))

# how much of each district lies inside flood zones?
flooded = gpd.overlay(districts, flood_zones, how='intersection')
flooded['flood_km2'] = flooded.area / 1e6

islands = coast.explode(ignore_index=True)

random_pts = parks.sample_points(10)         # 10 random points in each park
```

**Exercises**

1. Create 1 km buffers around hospitals and compute the share of the population living inside them.
2. Clip a national road layer to a province boundary and compute total road length.
3. Overlay land use with flood zones and calculate the area of each land-use type at risk.
4. Simplify a detailed coastline at three tolerances and compare vertex counts and visual quality.

## Module 7: Spatial relationships and spatial joins

Spatial joins attach attributes from one layer to another based on where features are, the geographic equivalent of a merge.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Spatial predicates | True/false tests between geometries | `intersects`, `within`, `contains`, `covers`, `covered_by`, `touches`, `crosses`, `overlaps`, `disjoint`, `contains_properly`, `dwithin` | Filtering by location |
| Boolean filtering | Use predicates as masks | `points[points.within(polygon)]` | Points in a zone |
| `sjoin` | Join rows where a predicate holds | `gpd.sjoin(left, right, how='left'/'inner'/'right', predicate='intersects'/'within'/'contains')` | Assign district to each address |
| Many-to-many results | One left row can match several right rows | Duplicates appear; aggregate after joining | Counting points per polygon |
| `sjoin_nearest` | Join to the closest feature | `gpd.sjoin_nearest(a, b, how='left', max_distance=, distance_col='dist')` | Nearest hospital or station |
| Distance-based joins | Within a distance | `predicate='dwithin', distance=` (GeoPandas 1.x) or buffer then `sjoin` | Facilities within 5 km |
| Counting points in polygons | Join then group | `joined.groupby('index_right').size()` | Incidents per district |
| Spatial index | R-tree for fast candidate search | `gdf.sindex`, `sindex.query(geom, predicate=)`, `sindex.nearest` | Large layers |
| Pairwise queries | Which features relate to which | `a.sindex.query(b.geometry, predicate='intersects')` returns index pairs | Custom workflows |
| Anti-join | Features with no match | `sjoin(...)` then keep `index_right.isna()` | Areas with no coverage |
| Overlay vs sjoin | `sjoin` keeps original geometries and adds attributes; `overlay` creates new geometries | Choose the right tool | Attribute lookup vs area split |
| Topology checks | Gaps, overlaps, duplicates | `overlay(how='intersection')`, `sjoin` of a layer with itself | Data quality |
| Matching CRS | Both layers must share a CRS | `to_crs` first | Avoiding empty results |

```python
addresses = addresses.to_crs(districts.crs)

# which district is each address in?
joined = gpd.sjoin(addresses, districts[['district', 'geometry']],
                   how='left', predicate='within')

# how many incidents per district?
counts = (gpd.sjoin(incidents, districts, predicate='within')
            .groupby('district').size().rename('n_incidents'))
districts = districts.merge(counts, on='district', how='left').fillna({'n_incidents': 0})

# nearest hospital to each home, with distance in metres
nearest = gpd.sjoin_nearest(homes.to_crs(32644), hospitals.to_crs(32644),
                            how='left', distance_col='dist_m')

# raw index pairs from the spatial index
left_idx, right_idx = parks.sindex.query(trees.geometry, predicate='within')
```

**Exercises**

1. Assign each store to its sales region and compute revenue per region.
2. Count the points of interest in every grid cell and map the counts.
3. Find the nearest fire station to each building and list those further than 3 km.
4. Identify districts that contain no clinics using an anti-join.

## Module 8: Attribute work: dissolve, groupby, merge and indexing

GeoPandas is pandas with geometry, so every pandas skill applies. This module covers the spatially aware versions.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Attribute filtering | Boolean masks, `query`, `isin` | `gdf[gdf['pop'] > 1e5]`, `gdf.query('type == "park"')` | Subset features |
| Attribute joins | Add table data by key (no geometry needed on the right) | `gdf.merge(df, on='code', how='left', validate='one_to_one')` | Census data on boundaries |
| Key hygiene | Match codes (strip, zero-pad, same dtype) before merging | `astype(str).str.zfill(5)` | Avoiding unmatched rows |
| `dissolve` | Group by an attribute and merge the geometries | `gdf.dissolve(by='region', aggfunc='sum', as_index=False)` | Districts into provinces |
| Aggregation functions | Choose per-column summaries | `aggfunc={'pop': 'sum', 'area': 'sum', 'name': 'first'}` | Preserve useful attributes |
| `groupby` | Ordinary statistics, then re-attach geometry | `gdf.groupby('type')['area'].sum()` | Summary tables |
| `explode` | One row per part | `gdf.explode(ignore_index=True, index_parts=False)` | Islands, multi-part roads |
| Derived columns | Spatial measures as attributes | `gdf['density'] = gdf['pop'] / gdf.area` | Maps of rates |
| Normalising for mapping | Rates not raw counts | `gdf['per_1000'] = gdf['cases'] / gdf['pop'] * 1000` | Fair comparisons |
| Sorting and ranking | Order by attributes or size | `sort_values`, `nlargest`, `rank` | Top areas |
| Index and reset | Keep tidy indexes after operations | `reset_index(drop=True)` | Predictable joins |
| Missing and duplicate rows | Clean features | `drop_duplicates(subset=...)`, `dropna`, `duplicated` | Data quality |
| Combining layers | Stack layers with the same schema | `pd.concat([a, b], ignore_index=True)` (same CRS) | Merge tiles or regions |
| Type conversions | Numeric and date columns | `astype`, `pd.to_datetime`, `to_numeric` | Clean attributes |
| Saving derived results | Write with new columns | `to_file`, `to_parquet` | Sharing outputs |

```python
census = pd.read_csv('census.csv', dtype={'district_code': str})
districts['district_code'] = districts['district_code'].astype(str).str.zfill(2)

map_df = districts.merge(census, on='district_code', how='left', validate='one_to_one')
map_df['density_km2'] = map_df['population'] / (map_df.to_crs(32644).area / 1e6)

provinces = (map_df.dissolve(by='province', aggfunc={'population': 'sum'})
                   .reset_index())

totals = map_df.groupby('province')['population'].sum().sort_values(ascending=False)
combined = pd.concat([north, south], ignore_index=True)
```

**Exercises**

1. Join a CSV of census figures onto boundary polygons and diagnose any rows that failed to match.
2. Dissolve districts into provinces, summing population and keeping the province name.
3. Compute cases per 1,000 residents and rank the top ten areas.
4. Combine two regional layers into one and confirm their CRS match first.

## Module 9: Visualisation and interactive maps

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Quick map | Plot geometries with Matplotlib | `gdf.plot(ax=, figsize=, color=, edgecolor=, linewidth=, alpha=)` | First look |
| Choropleth | Colour polygons by a value | `gdf.plot(column='density', cmap='viridis', legend=True, legend_kwds={'label': ...}, missing_kwds={'color': 'lightgrey'})` | Rates and totals by region |
| Classification schemes | Bin values (needs `mapclassify`) | `scheme='quantiles'/'equal_interval'/'natural_breaks'/'fisher_jenks'/'user_defined'`, `k=5`, `classification_kwds` | Readable class breaks |
| Colour choices | Sequential for magnitudes; diverging for values around a centre | `cmap='Blues'`, `'RdBu'`, `'viridis'` | Honest maps |
| Layering | Several layers on one axes | `ax = base.plot(); points.plot(ax=ax)` | Points over polygons |
| Marker size and colour | Encode attributes on points | `markersize=gdf['pop'] / 1e4`, `column=` | Bubble maps |
| Lines and networks | Style by attribute | `roads.plot(column='type', linewidth=roads['lanes'])` | Road maps |
| Basemaps | Add web tiles under data (reproject to EPSG:3857 for contextily) | `contextily.add_basemap(ax, crs=gdf.crs, source=...)` | Context for readers |
| Interactive maps | Zoom, pan, tooltips (needs folium and mapclassify) | `gdf.explore(column=, cmap=, tooltip=, popup=, tiles=, legend=True, scheme=)` | Exploration, sharing |
| Combining interactive layers | Add layers to one map | `m = a.explore(); b.explore(m=m)` | Multi-layer web map |
| Map polish | Title, axes off, scale and north arrow, attribution | `ax.set_axis_off()`, `ax.set_title()`, `matplotlib_scalebar` | Publication maps |
| Multi-panel maps | Compare variables | `fig, axs = plt.subplots(1, 3)` with `ax=axs[i]` | Small multiples |
| Zoom to extent | Limit view | `ax.set_xlim`, `ax.set_ylim`, `gdf.total_bounds` | Focus areas |
| Exporting | Save images and web maps | `fig.savefig('map.png', dpi=300, bbox_inches='tight')`, `m.save('map.html')` | Reports, websites |
| Other tools | Larger map apps and 3D | folium, kepler.gl, pydeck, lonboard, Plotly maps | Dashboards |

```python
import contextily as cx

fig, ax = plt.subplots(figsize=(8, 8))
map_df.to_crs(3857).plot(
    column='density_km2', scheme='quantiles', k=5, cmap='YlOrRd',
    legend=True, legend_kwds={'title': 'People per km2'},
    edgecolor='white', linewidth=.4, alpha=.8, ax=ax,
    missing_kwds={'color': 'lightgrey', 'label': 'No data'},
)
cx.add_basemap(ax, source=cx.providers.CartoDB.Positron)
ax.set_axis_off()
ax.set_title('Population density', fontsize=14)
fig.savefig('density.png', dpi=300, bbox_inches='tight')

# interactive version
m = map_df.explore(column='density_km2', cmap='YlOrRd', scheme='quantiles',
                   tooltip=['district', 'density_km2'], legend=True)
m.save('density.html')
```

Map-making guidance: map rates (per capita or per area) rather than raw counts, because large regions otherwise dominate; choose class breaks deliberately; always label units and data sources.

**Exercises**

1. Draw a choropleth of a rate with quantile and equal-interval classes and compare the stories they tell.
2. Plot points over polygon boundaries and add a basemap.
3. Build an interactive map with tooltips and export it as a standalone HTML file.
4. Create a three-panel figure showing the same area for three years.

## Module 10: Ecosystem integration

GeoPandas rarely works alone. These companions cover rasters, street networks, addresses, databases and web maps.

| Area | Library | What it does | Typical GeoPandas hand-off |
| --- | --- | --- | --- |
| Rasters | `rasterio`, `rioxarray` | Read and write grids such as elevation, satellite imagery, land cover | Mask rasters with polygons; sample raster values at points |
| Zonal statistics | `rasterstats.zonal_stats(polygons, raster)` | Mean, sum, count of raster cells per polygon | Add statistics as columns |
| Rasterising vectors | `rasterio.features.rasterize` | Burn polygons into a grid | Model inputs |
| Vectorising rasters | `rasterio.features.shapes` | Turn grid regions into polygons | Land-cover polygons |
| Street networks | `osmnx` | Download OpenStreetMap roads, buildings, points of interest; build routable graphs | `ox.features_from_place(...)` returns a GeoDataFrame; `ox.graph_to_gdfs` |
| Routing and graphs | `networkx`, `pandana`, `momepy` | Shortest paths, accessibility, urban morphology metrics | Convert lines to graphs and back |
| Geocoding | `geopy`, `geopandas.tools.geocode`, `geocode` services | Address to coordinates and the reverse | Build point layers from address lists (respect usage limits) |
| Spatial statistics | `pysal` family: `libpysal`, `esda`, `spreg`, `mapclassify` | Spatial weights, autocorrelation (Moran's I), hotspots, spatial regression | Weights from polygons |
| Spatial databases | PostGIS, SpatiaLite | Store and query large spatial data with SQL | `read_postgis`, `to_postgis` |
| Cloud-native formats | GeoParquet, FlatGeobuf, PMTiles, COG | Efficient remote access | `read_parquet` with filters |
| Web maps | `folium`, `leafmap`, `ipyleaflet`, `pydeck`, `lonboard` | Interactive and large-scale display | `gdf.explore()`, GeoJSON export |
| Geometry engine | `shapely` 2.x | Fast vectorised geometry operations | Direct calls on arrays |
| Coordinate engine | `pyproj` | Transformations and geodesic calculations (`Geod`) | Geodesic length and area |
| Satellite and earth data | `xarray`, `planetary-computer`, `earthengine-api` | Time series rasters | Extract values by polygon |
| Parallel geospatial | `dask-geopandas`, `spatialpandas` | Partitioned spatial dataframes | Large data (Module 11) |
| H3 and grid systems | `h3`, `s2`, `mercantile` | Hexagonal and tile indexes | Aggregate points to regular cells |

```python
import osmnx as ox
import rasterio
from rasterstats import zonal_stats

# OSM features as GeoDataFrames
cafes = ox.features_from_place('Colombo, Sri Lanka', tags={'amenity': 'cafe'})
buildings = ox.features_from_place('Fort, Colombo', tags={'building': True})

# elevation sampled at points
with rasterio.open('dem.tif') as src:
    coords = [(p.x, p.y) for p in points.to_crs(src.crs).geometry]
    points['elev'] = [v[0] for v in src.sample(coords)]

# mean raster value inside each polygon
stats = zonal_stats(districts, 'rainfall.tif', stats=['mean', 'max'], geojson_out=False)
districts['rain_mean'] = [s['mean'] for s in stats]

# geocode addresses (rate-limited public service)
from geopandas.tools import geocode
places = geocode(['Galle Fort, Sri Lanka'], provider='nominatim',
                 user_agent='my-geo-course')
```

Ethics and terms: respect geocoder and OpenStreetMap usage policies, credit data sources, and avoid publishing precise personal locations.

**Exercises**

1. Download cafes or schools for a city from OpenStreetMap and plot them over a district map.
2. Compute mean elevation or rainfall per district using a raster and `zonal_stats`.
3. Geocode ten addresses, check the results visually, and handle any failures.
4. Calculate Moran's I for a rate by district using `esda` and a queen-contiguity weight.

## Module 11: Performance and scaling

Spatial operations can be expensive. Most slowdowns come from missing spatial indexes, complex geometries, repeated work or loading too much data.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Vectorise | Use GeoSeries methods on whole columns rather than looping over rows | `gdf.geometry.buffer(...)`, never `for geom in ...` with Shapely calls if a vectorised method exists | 10x to 100x faster |
| Spatial index | R-tree makes joins and queries scale; built on first use | `gdf.sindex`, `sindex.query`, `sindex.nearest` | Large joins |
| Choose the right join | `sjoin` uses the index; avoid cross joins of all pairs | `sjoin`, `sjoin_nearest(max_distance=)` | Millions of features |
| Limit work early | Filter by attributes and bounding box before heavy operations | `read_file(bbox=)`, `cx[]`, query first | Faster pipelines |
| Read less | Select columns, rows, area | `columns=`, `rows=`, `mask=`, `ignore_geometry=True` | Memory |
| Fast I/O | Use pyogrio and columnar formats | `engine='pyogrio'`, `use_arrow=True`, GeoParquet, Feather | Quick reloads |
| Simplify and reduce precision | Fewer vertices and coordinates | `simplify`, `set_precision(grid_size=)`, `segmentize` for the opposite | Lighter data |
| Fix geometry once | `make_valid` early, not inside loops | `make_valid()` | Avoiding repeated errors |
| Overlay costs | Overlay on complex polygons is slow; clip first, dissolve or simplify, process in tiles | `clip`, grid chunking | Large overlays |
| Memory | Choose compact dtypes and drop unused columns | `astype('category')`, `drop` | Less RAM |
| Parallel and out-of-core | Partition by space and process in parallel | `dask-geopandas`, `spatialpandas`, chunk loops over a grid | Beyond memory |
| Databases | Push spatial queries to PostGIS with indexes | `read_postgis(sql)` with `ST_Intersects`, GiST index | Shared large datasets |
| Caching | Store intermediate results | `to_parquet`, `to_feather` | Long workflows |
| Profiling | Measure first | `%timeit`, `cProfile`, `tracemalloc` | Finding bottlenecks |
| Grid aggregation | Aggregate points to cells instead of joining millions to polygons | Grid or H3 index then `groupby` | Heatmaps of huge point sets |

```python
import time

# index-assisted pairwise query
left_idx, right_idx = zones.sindex.query(points.geometry, predicate='within')

# filter by area and columns while reading
roads = gpd.read_file('roads.gpkg', bbox=tuple(city.total_bounds),
                      columns=['name', 'class'], engine='pyogrio')

# fast intermediate storage
roads.to_parquet('roads.parquet')
roads = gpd.read_parquet('roads.parquet', columns=['class', 'geometry'])

# chunk a heavy overlay by tiles
results = []
for tile in tiles.geometry:
    a = gpd.clip(layer_a, tile)
    b = gpd.clip(layer_b, tile)
    results.append(gpd.overlay(a, b, how='intersection'))
out = pd.concat(results, ignore_index=True)

# parallel option
import dask_geopandas
dgdf = dask_geopandas.from_geopandas(gdf, npartitions=8)
dgdf = dgdf.spatial_shuffle()
```

**Exercises**

1. Time a point-in-polygon join of 1 million points with and without using `sjoin`, versus a Python loop on 10,000 points.
2. Cut the load time of a large layer in half using column selection, a bounding box and Parquet.
3. Run an overlay of two big layers in tiles and compare the time and memory with the full overlay.

## Module 12: Real-world use cases and capstone projects

**Use cases by domain**

| Domain | Typical questions | Core tools |
| --- | --- | --- |
| Urban planning | Which neighbourhoods lack a park within 10 minutes' walk? How dense is each ward? | buffers, `sjoin`, `overlay`, `dissolve` |
| Retail and logistics | Which stores serve which customers? Where should the next depot go? | `sjoin_nearest`, Voronoi, catchment buffers |
| Public health | Where are disease rates highest? Who lives far from a clinic? | choropleths, `sjoin_nearest`, spatial statistics |
| Environment and climate | How much forest overlaps protected areas? Which villages lie in flood zones? | `overlay`, zonal statistics, raster hand-off |
| Real estate and insurance | Property exposure to hazards, price by neighbourhood | `sjoin`, buffers, `overlay` |
| Transport | Road length per district, stop coverage, accessibility | line `length`, OSMnx, graphs |
| Agriculture | Field boundaries, soil and rainfall per plot | zonal stats, `overlay` |
| Emergency response | Nearest station, response coverage | `sjoin_nearest`, service areas |
| Elections and demographics | Boundary changes, population by district, compactness | `dissolve`, area measures |
| Telecoms | Tower coverage and gaps | buffers, `difference`, `union_all` |

**Capstone projects**

| Project | Data ideas | Skills tested | Deliverable |
| --- | --- | --- | --- |
| 1. Access to services | Population grid or points, clinics or schools, district boundaries | CRS, buffers, `sjoin`, `sjoin_nearest`, choropleth | Map and table of people living more than 5 km from a service |
| 2. Flood exposure analysis | Flood zone polygons, buildings or land use, districts | `clip`, `overlay`, area calculations, dissolve | Report of exposed area and share per district with a map |
| 3. Retail catchments | Store points, customer points | Voronoi, buffers, spatial joins, aggregation | Interactive map of catchments with revenue per catchment |
| 4. Regional dashboard map | Census table plus boundaries | Attribute join, density rates, classification, `explore` | Interactive HTML choropleth with tooltips |
| 5. Hotspot detection | Incident points (crime, accidents) | Grid or H3 aggregation, `esda` Moran's I and Getis-Ord | Map of statistically significant clusters |
| 6. Street network analysis | OSM roads and amenities | OSMnx, line lengths, nearest joins | Accessibility map and a short written finding |

```python
# Capstone 1 core: who lives more than 5 km from a clinic?
homes = homes.to_crs(32644)
clinics = clinics.to_crs(32644)

near = gpd.sjoin_nearest(homes, clinics[['clinic_id', 'geometry']],
                         how='left', distance_col='dist_m')
far = near[near['dist_m'] > 5000]

summary = (far.groupby('district')['household_size'].sum()
              .div(homes.groupby('district')['household_size'].sum())
              .rename('share_far').reset_index())

districts.merge(summary, on='district').plot(
    column='share_far', cmap='OrRd', legend=True, scheme='quantiles')
```

**Project hygiene:** record the CRS used for every measurement, keep raw data separate from derived layers, store outputs in GeoPackage or GeoParquet, write a short methods note (data sources, dates, projections, limitations), and check results against known figures.

**Practice sources:** Natural Earth, OpenStreetMap through OSMnx, national census and boundary portals, the HDX humanitarian data exchange, and the GeoPandas and PySAL gallery examples.

## Appendix: cheat sheet, pitfalls, checklist and resources

**Quick reference by task**

| I want to... | Use |
| --- | --- |
| Load a file | `gpd.read_file(path, bbox=, columns=)`, `read_parquet` |
| Make points from columns | `gpd.points_from_xy(lon, lat, crs=4326)` |
| Check or set the CRS | `gdf.crs`, `set_crs`, `to_crs`, `estimate_utm_crs()` |
| Measure | `area`, `length`, `distance`, `bounds`, `total_bounds`, `centroid` |
| Fix geometry | `is_valid`, `make_valid()`, `explode`, `simplify` |
| Make zones | `buffer`, `convex_hull`, `voronoi_polygons`, `union_all` |
| Cut and combine | `clip`, `overlay`, `dissolve`, `pd.concat` |
| Join by location | `sjoin`, `sjoin_nearest`, `sindex.query` |
| Join by attribute | `merge` on a clean key |
| Map it | `plot(column=, scheme=, legend=True)`, `explore()`, `contextily.add_basemap` |
| Save | `to_file(driver='GPKG')`, `to_parquet`, `to_file(driver='GeoJSON')` |

**Common pitfalls**

1. Measuring distance or area in degrees (EPSG:4326). Reproject to a suitable projected CRS first.
2. Confusing `set_crs` (label) with `to_crs` (convert).
3. Joining or overlaying layers with different CRS, giving empty or wrong results. Check `a.crs == b.crs` every time.
4. Swapped coordinates: longitude is x and latitude is y.
5. Invalid geometries breaking overlays and unions. Run `make_valid()` first.
6. Forgetting that `sjoin` can duplicate left rows when several right features match.
7. Merging on keys with different types or missing leading zeros, leaving empty joined columns.
8. Mapping raw counts instead of rates, so large regions look misleadingly extreme.
9. Writing shapefiles with long column names and expecting them to survive; use GeoPackage instead.
10. Using renamed or removed APIs after upgrading (for example `unary_union` replaced by `union_all()`, and the removed bundled `gpd.datasets`). Read the release notes.
11. Treating `buffer` as geodesic: it works in CRS units, so a degree-based buffer is distorted.
12. Using web mercator (EPSG:3857) for area or length. It is for display only.

**8-week practice plan**

| Weeks | Daily habit (45 to 60 minutes) |
| --- | --- |
| 1 to 3 | Read a module, type every example, finish the exercises |
| 4 to 5 | Rebuild one map or analysis from a news story or open data portal each day |
| 6 to 7 | Apply the tools to a dataset from your own city or workplace; optimise it |
| 8 | Complete two capstones and publish the maps and methods note |

**Mastery checklist**

- [ ] I can explain geographic vs projected CRS and pick a suitable CRS for a measurement.
- [ ] I can build a GeoDataFrame from coordinates, WKT, GeoJSON or files.
- [ ] I can read and write shapefile, GeoJSON, GeoPackage and GeoParquet appropriately.
- [ ] I can measure areas, lengths and distances correctly and check geometry validity.
- [ ] I can buffer, clip, overlay, dissolve and union layers.
- [ ] I can choose between `sjoin`, `sjoin_nearest` and `overlay` for a question.
- [ ] I can attach census or other tabular data to boundaries without losing rows.
- [ ] I can produce a clear choropleth and an interactive map with sensible classes.
- [ ] I can bring in rasters, OSM data and geocoded points, and keep everything in one CRS.
- [ ] I can speed up a slow spatial workflow using indexes, filtering and efficient formats.

**Resources**

- Official GeoPandas documentation: user guide, gallery and API reference (geopandas.org)
- Shapely and pyproj documentation for geometry and coordinate details
- *Geographic Data Science with Python* (free online book by Rey, Arribas-Bel and Wolf)
- *Geocomputation with Python* (free online book)
- PySAL, OSMnx and rasterio documentation for the companion libraries
- Next steps: the pandas, Matplotlib and Seaborn courses in this series, then spatial statistics with PySAL and cloud-native raster analysis with xarray
