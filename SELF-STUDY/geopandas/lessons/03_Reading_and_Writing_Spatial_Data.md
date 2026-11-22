[⬅️ කලින් පාඩම: GeoSeries & Geometries](02_GeoSeries_GeoDataFrame_and_Geometries.md) | [🏠 Course Home](README.md) | [ඊළඟ පාඩම: CRS & Projections ➡️](04_CRS_and_Projections.md)

---

# 📘 පාඩම 03: Spatial Data Read සහ Write කරන හැටි (File Formats & I/O)

සැබෑ GIS හෝ Data Science project එකකදී අපිට විවිධ file formats එක්ක වැඩ කරන්න වෙනවා — පරණ රජයේ ආයතන වලින් එන **Shapefiles (`.shp`)**, web maps වලට ඕනේ **GeoJSON**, නවීන **GeoPackage (`.gpkg`)**, සහ big data analytics වලට සුපිරි **GeoParquet (`.parquet`)**.

GeoPandas වල තියෙන ලොකුම වාසිය තමයි මේ ගොඩක් formats එකම function දෙකකින් (`gpd.read_file()` සහ `gdf.to_file()`) ලේසියෙන්ම read සහ write කරන්න පුළුවන් වෙන එක!

---

## 🎯 මේ පාඩමෙන් ඔයා ඉගෙන ගන්න දේවල්

- GeoPandas 1.x වල default I/O engine එක (`pyogrio`) වැඩ කරන හැටි
- **Shapefile**, **GeoJSON**, **GeoPackage**, සහ **GeoParquet / Feather** අතර වෙනස සහ කොයි වෙලාවට මොන format එක තෝරගන්නවද කියලා
- Multi-layer GeoPackage (`.gpkg`) වල layers list කරන (`list_layers`) සහ read/write කරන හැටි
- KML, GPX, FlatGeobuf, ZIP files, සහ URLs වලින් කෙලින්ම data read කරන හැටි
- ලොකු files සම්පූර්ණයෙන්ම RAM එකට ගන්නේ නැතුව **Read Less** (`bbox`, `mask`, `rows`, `columns`, `where`, `ignore_geometry=True`) පාවිච්චි කරන හැටි
- CSV, PostGIS / SpatiaLite databases, Encoding (`utf-8` / `latin1`), Write options (`mode='a'`, `index=False`), සහ Round-trip checks

---

## 1. ප්‍රධාන Spatial File Formats සංසන්දනය

හැම file format එකකටම වාසි සහ අවාසි තියෙනවා. පහත රූපයෙන් ඒ 4 හොඳටම තේරුම් ගන්න පුළුවන්:

![Spatial File Formats Comparison](images/gpd_03_spatial_file_formats.png)

### කොයි Format එක තෝරගන්නද? (Decision Guide)

```mermaid
flowchart TD
    Q1{"What is your main goal?"}
    Q1 -->|"Web Maps / REST APIs"| GJ["GeoJSON (.geojson) — Reproject to EPSG:4326 first!"]
    Q1 -->|"Fast Python Analytics / Big Data"| GPQ["GeoParquet (.parquet) or Feather"]
    Q1 -->|"General GIS / Sharing / Multi-Layer"| GPKG["GeoPackage (.gpkg) — Modern Default!"]
    Q1 -->|"Old GIS Software Requires It"| SHP["Shapefile (.shp) — Watch 10-char limit & sidecars!"]
```

| Format | Read / Write API | විශේෂ කරුණු | හොඳම Use Case එක |
| --- | --- | --- | --- |
| **Any OGR Format** | `gpd.read_file(path)`, `gdf.to_file(path, driver=)` | GeoPandas 1.x වල default engine එක **`pyogrio`** (ඉතා වේගවත්; පරණ version වල `Fiona`) | බොහෝ formats සඳහා එකම function එක |
| **Shapefile (`.shp`)** | `read_file('a.shp')`, `to_file('a.shp')` | තනි file එකක් නෙවෙයි! `.shp`, `.shx`, `.dbf`, `.prj` sidecar files ඕනේ. **Column names අකුරු 10ට කැපෙනවා!** 2 GB limit. | Legacy GIS දත්ත |
| **GeoJSON** | `read_file('a.geojson')`, `to_file(..., driver='GeoJSON')`, `to_json()` | මිනිසුන්ට කියවන්න පුළුවන් JSON text. සාමාන්‍යයෙන් **`EPSG:4326`** බලාපොරොත්තු වෙනවා. | Web maps (Leaflet/Mapbox) සහ APIs |
| **GeoPackage (`.gpkg`)** | `to_file('a.gpkg', layer='roads', driver='GPKG')` | **තනි file එකක්**, ඇතුළේ **layers ගොඩක්** දාන්න පුළුවන්, 10-char limit නැහැ! | **Recommended Modern Default** |
| **GeoParquet / Feather** | `gpd.read_parquet`, `to_parquet`, `read_feather`, `to_feather` | Columnar binary format — ඉතාම වේගවත්, disk space හරිම අඩුයි. | Large analytical / ML datasets |
| **KML, GPX, FlatGeobuf** | `read_file` / `to_file(..., driver='KML' / 'GPX' / 'FlatGeobuf')` | OGR drivers හරහා වැඩ කරනවා. | Google Earth (KML), GPS tracks (GPX), cloud streaming (FlatGeobuf) |

---

## 2. සම්පූර්ණ Code උදාහරණය: Read, Subset, Multi-Format Export & Layers

```python
import geopandas as gpd

# 1. GeoPackage file එකක තියෙන නිශ්චිත layer එකක් ('roads') කියවන්න
roads = gpd.read_file('city.gpkg', layer='roads')

# 2. ලොකු file එකකින් අපිට ඕනේ ප්‍රදේශය (bbox) සහ columns විතරක් කියවන්න!
subset = gpd.read_file(
    'big.gpkg',
    bbox=(79.8, 6.8, 80.0, 7.0),   # (minx, miny, maxx, maxy) — කොළඹ අවට
    columns=['name', 'type']
)

# 3. විවිධ formats වලට save කරන්න
roads.to_file('out.gpkg', layer='roads_clean', driver='GPKG')   # GeoPackage (Modern default)
roads.to_parquet('roads.parquet')                                # GeoParquet (Fastest reload!)
roads.to_file('roads.geojson', driver='GeoJSON')                 # GeoJSON (Web maps)

# 4. GeoPackage එක ඇතුළේ තියෙන සියලුම layers බලන්න
print(gpd.list_layers('out.gpkg'))
```

### Line-by-Line පැහැදිලි කිරීම
- `gpd.read_file('city.gpkg', layer='roads')`: `city.gpkg` එක ඇතුළේ layers කිහිපයක් තියෙන්න පුළුවන් (`roads`, `buildings`, `parks`). ඒකෙන් `roads` විතරක් load කරනවා.
- `bbox=(79.8, 6.8, 80.0, 7.0)`: මුළු ලංකාවේම data තියෙන 1 GB file එකක් වුණත්, මේ කොටුව ඇතුළේ තියෙන features විතරක් disk එකෙන් කියවනවා — RAM එක ඉතුරුයි, speed එක වැඩියි!
- `columns=['name', 'type']`: අනවශ්‍ය attribute columns 50ක් තිබුණත් අපිට ඕනේ 2 විතරක් ගන්නවා (`geometry` එක auto එනවා).
- `roads.to_parquet('roads.parquet')`: ආපහු Python වලින් load කරන්න (`gpd.read_parquet('roads.parquet')`) 10x–50x වේගවත්!
- `gpd.list_layers('out.gpkg')`: `.gpkg` file එක ඇතුළේ තියෙන හැම layer එකේම නම සහ geometry type එක table එකක් විදිහට පෙන්නනවා.

### Expected Output (`gpd.list_layers`)
```text
          name geometry_type
0  roads_clean    LineString
```

---

## 3. "Read Less" — ලොකු Files වේගයෙන් Read කරන උපක්‍රම

ලොකු spatial file එකක් සම්පූර්ණයෙන්ම load නොකර, කියවන වෙලාවෙම filter කරන්න පුළුවන් parameters 6ක් `read_file()` වල තියෙනවා:

```mermaid
flowchart LR
    BIG["Huge Spatial File (2 GB .gpkg)"] --> F1["bbox=(minx, miny, maxx, maxy)"]
    BIG --> F2["mask=polygon_gdf"]
    BIG --> F3["rows=100 or slice(10, 50)"]
    BIG --> F4["columns=['name', 'pop']"]
    BIG --> F5["where=\"type = 'highway'\""]
    BIG --> F6["ignore_geometry=True (plain DataFrame)"]
```

```python
# 1. මුල් rows 100 විතරක් test කරන්න කියවන්න
sample = gpd.read_file('big.gpkg', rows=100)

# 2. Polygon mask එකක් ඇතුළේ තියෙන features විතරක් කියවන්න
inside_colombo = gpd.read_file('big.gpkg', mask=colombo_district_gdf)

# 3. SQL WHERE clause එකකින් filter කරලා කියවන්න
highways = gpd.read_file('big.gpkg', where="road_type = 'expressway'")

# 4. Geometry ඕනේ නැත්නම් (attributes විතරක් ඕනේ නම්) — ඉතා වේගවත්!
attrs_only = gpd.read_file('big.gpkg', ignore_geometry=True)
```

---

## 4. ZIP Files, URLs, CSV, Databases & Encoding

### A. ZIP Files සහ Web URLs වලින් කෙලින්ම Read කිරීම
File එක unzip කරන්න හෝ මුලින් download කරන්න ඕනේ නැහැ!

```python
# ZIP file එකක් ඇතුළේ තියෙන shapefile එකක් කෙලින්ම read කරන්න
gdf_zip = gpd.read_file('zip://sri_lanka_districts.zip')

# Web URL එකකින් GeoJSON එකක් කෙලින්ම read කරන්න
url = 'https://raw.githubusercontent.com/datasets/geo-countries/master/data/countries.geojson'
gdf_web = gpd.read_file(url)
```

### B. CSV with Geometry
CSV එකක `lon`/`lat` හෝ `wkt` තියෙනවා නම්:
```python
df = pd.read_csv('locations.csv')
# lon/lat නම්:
gdf = gpd.GeoDataFrame(df, geometry=gpd.points_from_xy(df['lon'], df['lat']), crs='EPSG:4326')
# WKT නම්:
# gdf = gpd.GeoDataFrame(df, geometry=gpd.GeoSeries.from_wkt(df['wkt']), crs='EPSG:4326')
```

### C. Spatial Databases (PostGIS & SpatiaLite)
Production systems වල data තියෙන්නේ PostgreSQL + **PostGIS** වගේ databases වල:

```python
from sqlalchemy import create_engine

engine = create_engine('postgresql://user:password@localhost:5432/gis_db')

# PostGIS වලින් SQL query එකකින් read කරන්න
sql = "SELECT id, name, geom FROM roads WHERE district = 'Colombo'"
roads_db = gpd.read_postgis(sql, con=engine, geom_col='geom')

# PostGIS වලට write කරන්න
roads_db.to_postgis('roads_colombo', con=engine, if_exists='replace')
```

### D. Encoding (සිංහල / Non-ASCII නම් හරියට කියවන්න)
සිංහල හෝ දෙමළ නගර නම් තියෙන files වල අකුරු අවුල් වෙනවා නම් `encoding` parameter එක දෙන්න:

```python
gdf_sl = gpd.read_file('gn_divisions.shp', encoding='utf-8')
# පරණ Windows shapefiles වල සමහරවිට encoding='latin1' අවශ්‍ය වෙනවා
```

### E. Write Options & Round-Trip Checks

```python
# දැනටමත් තියෙන file එකකට අලුත් rows append කරන්න (index column එක නැතුව)
more_roads.to_file('out.gpkg', layer='roads_clean', mode='a', index=False, engine='pyogrio')

# Round-trip check — save කරපු file එක ආපහු කියවලා data හරියටම තියෙනවද verify කරන්න!
reloaded = gpd.read_file('out.gpkg', layer='roads_clean')
assert reloaded.crs == roads.crs, "CRS mismatch!"
assert len(reloaded) == len(roads), "Row count mismatch!"
print("Dtypes:\n", reloaded.dtypes)
```

---

## 🧠 මතක තබා ගැනීමට පහසු ක්‍රම (Memory Hacks)

1. **"GPKG for Sharing, Parquet for Speed, GeoJSON for Web":**
   - වෙන GIS කෙනෙක්ට (QGIS/ArcGIS) යවන්න → **GeoPackage (`.gpkg`)**
   - ඔයාගේ Python/ML pipeline එකට → **GeoParquet (`.parquet`)**
   - Website / JavaScript map එකකට → **GeoJSON (`.geojson`)**
2. **Shapefile = "10-Letter Dinosaur":** Column names අකුරු 10න් කැපෙනවා (`population_2026` → `population`), files 4-5ක් එකට යවන්න ඕනේ. පුළුවන් හැමවෙලේම `.gpkg` පාවිච්චි කරන්න!

## ⚠️ සාමාන්‍යයෙන් වෙන වැරදි (Common Pitfalls)

> [!WARNING]
> **1. Shapefile එකේ `.shp` file එක විතරක් email/copy කරන එක:**
> `.shp` එක තනියම වැඩ කරන්නේ නැහැ! `.dbf` (attributes), `.shx` (index), සහ `.prj` (CRS) නැතිවුණොත් file එක corrupt වෙනවා හෝ CRS එක නැතිවෙනවා. හැමවෙලේම ඔක්කොම sidecar files ZIP කරලා යවන්න (හෝ `.gpkg` පාවිච්චි කරන්න).

> [!WARNING]
> **2. Projected CRS එකකින් GeoJSON export කරන එක:**
> GeoJSON specification එකට අනුව coordinates තියෙන්න ඕනේ **`EPSG:4326`** (lon/lat) වලින්. Web map එකකට export කරන්න කලින් `gdf.to_crs('EPSG:4326').to_file('map.geojson', driver='GeoJSON')` කරන්න!

> [!WARNING]
> **3. Shapefile save කරද්දී Column Names කැපෙන එක:**
> `district_population_male` සහ `district_population_female` දෙකම Shapefile වලදී අකුරු 10ට කැපිලා `district_p`, `district_1` වගේ අවුල් නම් හැදෙනවා.

---

## 🏋️ ප්‍රායෝගික අභ්‍යාස (Practical Exercises)

### Warm-up Exercise: Round-Trip Integrity Check
`geodatasets` වලින් `nybb` load කරලා `nybb.gpkg` විදිහට save කරලා, ආපහු read කරලා CRS සහ row count සමානද කියලා `assert` වලින් verify කරන්න.

<details>
<summary>✅ පිළිතුර බලන්න (Click to see Solution)</summary>

```python
import geopandas as gpd
import geodatasets

nyc = gpd.read_file(geodatasets.get_path('nybb'))
nyc.to_file('nybb.gpkg', layer='boroughs', driver='GPKG')

reloaded = gpd.read_file('nybb.gpkg', layer='boroughs')
assert reloaded.crs == nyc.crs
assert len(reloaded) == len(nyc)
print("Round-trip check PASSED! Rows:", len(reloaded))
```
</details>

### Exercise 1: Shapefile → GeoPackage & GeoParquet (File Size & Read Time සංසන්දනය)
එක shapefile එකක් (උදා: `nybb`) `.shp`, `.gpkg`, සහ `.parquet` විදිහට save කරලා, ඒ තුනේ file sizes සහ read කරන්න යන වෙලාව compare කරන්න.

<details>
<summary>✅ පිළිතුර බලන්න (Click to see Solution)</summary>

```python
import os
import time
import geopandas as gpd
import geodatasets

nyc = gpd.read_file(geodatasets.get_path('nybb'))

# 1. තුන් ආකාරයට save කරන්න
nyc.to_file('nyc.shp')
nyc.to_file('nyc.gpkg', layer='nyc', driver='GPKG')
nyc.to_parquet('nyc.parquet')

# 2. File sizes සහ read times compare කරන්න
for label, path, reader in [
    ('Shapefile',  'nyc.shp',     gpd.read_file),
    ('GeoPackage', 'nyc.gpkg',    gpd.read_file),
    ('GeoParquet', 'nyc.parquet', gpd.read_parquet),
]:
    size_kb = os.path.getsize(path) / 1024
    t0 = time.perf_counter()
    _ = reader(path)
    elapsed_ms = (time.perf_counter() - t0) * 1000
    print(f"{label:12s} | Size: {size_kb:7.1f} KB | Read time: {elapsed_ms:6.2f} ms")
```
</details>

### Exercise 2: Bounding Box එකකින් විතරක් Read කරන්න
ලොකු file එකකින් (හෝ `nyc.gpkg` එකෙන්) `bbox` parameter එක පාවිච්චි කරලා නිශ්චිත කොටුවක් ඇතුළේ තියෙන features විතරක් read කරන්න.

<details>
<summary>✅ පිළිතුර බලන්න (Click to see Solution)</summary>

```python
import geopandas as gpd

# nyc.gpkg එකේ total_bounds වලින් කොටසක් (උදා: Manhattan අවට bbox එක)
subset = gpd.read_file(
    'nyc.gpkg',
    bbox=(975000, 190000, 1010000, 235000),
    columns=['BoroName', 'Shape_Area']
)
print(subset)
```
</details>

### Exercise 3: එක GeoPackage එකක Layers 3ක් Save කරලා List කරන්න
ලංකාවේ නගර (`cities`), පාරවල් (`roads`), සහ කලාප (`zones`) කියන GeoDataFrames 3ම එකම `sri_lanka.gpkg` file එකේ layers 3ක් විදිහට save කරලා `gpd.list_layers()` වලින් list කරන්න.

<details>
<summary>✅ පිළිතුර බලන්න (Click to see Solution)</summary>

```python
import geopandas as gpd
from shapely.geometry import Point, LineString, box

cities = gpd.GeoDataFrame({'name': ['Colombo', 'Kandy']},
                          geometry=[Point(79.86, 6.93), Point(80.63, 7.29)], crs='EPSG:4326')
roads  = gpd.GeoDataFrame({'name': ['A1 Highway']},
                          geometry=[LineString([(79.86, 6.93), (80.63, 7.29)])], crs='EPSG:4326')
zones  = gpd.GeoDataFrame({'name': ['Western Box']},
                          geometry=[box(79.5, 6.5, 80.5, 7.5)], crs='EPSG:4326')

cities.to_file('sri_lanka.gpkg', layer='cities', driver='GPKG')
roads.to_file('sri_lanka.gpkg',  layer='roads',  driver='GPKG')
zones.to_file('sri_lanka.gpkg',  layer='zones',  driver='GPKG')

print(gpd.list_layers('sri_lanka.gpkg'))
```
</details>

### Exercise 4: GeoJSON Export කරලා Web Viewer එකක බලන්න
`nyc` GeoDataFrame එක `EPSG:4326` වලට reproject කරලා `nyc_web.geojson` විදිහට save කරන්න (ඒ file එක [geojson.io](https://geojson.io) වෙබ් අඩවියට drag-and-drop කරලා interactive map එක බලන්න පුළුවන්!).

<details>
<summary>✅ පිළිතුර බලන්න (Click to see Solution)</summary>

```python
import geopandas as gpd
import geodatasets

nyc = gpd.read_file(geodatasets.get_path('nybb'))

# Web maps (GeoJSON) සඳහා අනිවාර්යයෙන්ම EPSG:4326 වලට හරවන්න!
nyc_4326 = nyc.to_crs('EPSG:4326')
nyc_4326.to_file('nyc_web.geojson', driver='GeoJSON')
print("Saved nyc_web.geojson in EPSG:4326 — ready for geojson.io!")
```
</details>

---

## 📋 Cheat Sheet Summary (කෙටි සටහන)

| කාර්යය | Key API |
| --- | --- |
| Read any OGR file | `gpd.read_file(path)` |
| Write GeoPackage (Best default) | `gdf.to_file('out.gpkg', layer='name', driver='GPKG')` |
| List layers in GeoPackage | `gpd.list_layers('out.gpkg')` |
| Fast binary Parquet / Feather | `gpd.read_parquet` / `to_parquet`, `read_feather` / `to_feather` |
| Web GeoJSON export | `gdf.to_crs(4326).to_file('out.geojson', driver='GeoJSON')` |
| Read ZIP or URL | `gpd.read_file('zip://data.zip')`, `gpd.read_file('https://.../a.geojson')` |
| Read less (Large files) | `gpd.read_file(path, bbox=, mask=, rows=, columns=, where=, ignore_geometry=True)` |
| Read / Write PostGIS | `gpd.read_postgis(sql, con, geom_col='geom')`, `gdf.to_postgis(name, engine, if_exists=)` |
| Fix Sinhala / text encoding | `gpd.read_file(path, encoding='utf-8')` |
| Append without index | `gdf.to_file('out.gpkg', layer='x', mode='a', index=False)` |

---

[⬅️ කලින් පාඩම: GeoSeries & Geometries](02_GeoSeries_GeoDataFrame_and_Geometries.md) | [🏠 Course Home](README.md) | [ඊළඟ පාඩම: CRS & Projections ➡️](04_CRS_and_Projections.md)
