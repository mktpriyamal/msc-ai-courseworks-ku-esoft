[🏠 පාඨමාලා මුල් පිටුව (Course Index)](README.md) | [⬅️ පෙර පාඩම: 09 Visualisation & Interactive Maps](09_Visualisation_and_Interactive_Maps.md) | [ඊළඟ පාඩම: 11 Performance & Scaling ➡️](11_Performance_and_Scaling.md)

---

# 10. Ecosystem Integration (GeoPandas අනෙක් Spatial Libraries එක්ක එකට වැඩ කරන හැටි)

> **🎯 මේ පාඩමෙන් ඔයා ඉගෙන ගන්න දේවල්:**
> - GeoPandas තනියම නෙවෙයි, Python Geospatial Ecosystem එකේ අනෙක් ප්‍රබල libraries 16ක් එක්ක සම්බන්ධ වෙලා වැඩ කරන විදිහ.
> - `rasterio`, `rioxarray`, සහ `rasterstats` (`zonal_stats`) පාවිච්චි කරලා Raster data (elevation, rainfall, satellite images) GeoPandas vector data එක්ක එකතු කරන හැටි.
> - `osmnx` වලින් OpenStreetMap (OSM) data (පාරවල්, ගොඩනැගිලි, කැෆේ, පාසල්) කෙලින්ම GeoDataFrame එකක් විදිහට download කරන හැටි.
> - `geopy` සහ `geopandas.tools.geocode` වලින් ලිපින (addresses) coordinates වලට හරවන හැටි (Geocoding).
> - `PySAL` (`libpysal`, `esda`, `spreg`, `mapclassify`), `PostGIS`, `GeoParquet`, `H3`, `pyproj` (`Geod`), සහ `shapely` 2.x ගැන සම්පූර්ණ අවබෝධයක්.
> - OpenStreetMap සහ Geocoding පාවිච්චි කරද්දී අනුගමනය කරන්න ඕන Ethics සහ Terms of Service.

---

## 1. GeoPandas Ecosystem එක කියන්නේ මොකක්ද?

GeoPandas කියන්නේ Vector data (Points, Lines, Polygons) handle කරන්න සුපිරිම tool එකක්. හැබැයි ඇත්ත ජීවිතේ project එකක් කරද්දී අපිට vector data විතරක් මදි වෙනවා!

හිතන්න ඔයාට ඕන "කොළඹ දිස්ත්‍රික්කයේ තියෙන කැෆේ ටික අරගෙන, ඒ හැම තැනකම මුහුදු මට්ටමේ ඉඳන් උස (Elevation) සහ ඒ දිස්ත්‍රික්කයේ සාමාන්‍ය වර්ෂාපතනය (Rainfall) ගණන් හදලා, PostGIS database එකක save කරන්න."
මේ වැඩේට GeoPandas තනියම මදි. ඒකට අනෙක් libraries උදව්වට ගන්න ඕන:

```mermaid
flowchart TB
    GPD["GeoPandas (Core Vector GeoDataFrame)"]

    GPD <-->|"Mask & Sample"| RAS["Rasters (rasterio, rioxarray, rasterstats)"]
    GPD <-->|"OSM Roads & POIs"| OSM["Street Networks (osmnx, networkx, pandana, momepy)"]
    GPD <-->|"Address <-> Lat/Lon"| GEO["Geocoding (geopy, geopandas.tools.geocode)"]
    GPD <-->|"Spatial Stats & Hotspots"| PYSAL["PySAL (libpysal, esda, spreg, mapclassify)"]
    GPD <-->|"SQL & Cloud Storage"| DB["Databases & Cloud (PostGIS, SpatiaLite, GeoParquet)"]
    GPD <-->|"Hex Grids & Tiles"| GRID["Grid Systems (h3, s2, mercantile)"]
    GPD <-->|"Interactive Web Maps"| WEB["Web Maps (folium, leafmap, pydeck, lonboard)"]
    GPD <-->|"Core Engines & Scale"| ENG["Engines & Parallel (shapely 2.x, pyproj, dask-geopandas)"]
```

---

## 2. සම්පූර්ණ Ecosystem Library වගුව (16 Companion Areas)

GeoPandas එක්ක වැඩ කරන ප්‍රධාන areas 16 සහ ඒවායේ hand-off (දත්ත හුවමාරු කරගන්න) විදිහ පහළ වගුවෙන් බලමු:

| Area | Library | මොකක්ද කරන්නේ? (What it does) | GeoPandas Hand-off එක |
| --- | --- | --- | --- |
| **Rasters** | `rasterio`, `rioxarray` | Elevation (DEM), satellite imagery, land cover වගේ grid data කියවන්න සහ ලියන්න | Polygons වලින් rasters mask කරනවා; points තියෙන තැන්වල raster values ගන්නවා (`sample`) |
| **Zonal statistics** | `rasterstats.zonal_stats(polygons, raster)` | හැම polygon එකක් ඇතුළෙම තියෙන raster cells වල `mean`, `sum`, `max`, `count` හොයනවා | ලැබෙන stats ටික GeoDataFrame එකට අලුත් columns විදිහට එකතු කරනවා |
| **Rasterising vectors** | `rasterio.features.rasterize` | Vector polygons අරගෙන raster grid එකකට "burn" (පරිවර්තනය) කරනවා | Machine learning / hydrological models වලට grid inputs හදන්න |
| **Vectorising rasters** | `rasterio.features.shapes` | Raster grid එකේ එකම අගය තියෙන ප්‍රදේශ polygons බවට හරවනවා | Land-cover හෝ flood mask rasters වලින් polygons හදන්න |
| **Street networks** | `osmnx` | OpenStreetMap එකෙන් පාරවල්, ගොඩනැගිලි, POIs (cafes, schools) download කරලා routable graphs හදනවා | `ox.features_from_place(...)` කෙලින්ම GeoDataFrame එකක් දෙනවා; `ox.graph_to_gdfs()` |
| **Routing and graphs** | `networkx`, `pandana`, `momepy` | කෙටිම පාර (shortest path), accessibility, urban morphology මනිනවා | LineStrings ටික graph (nodes + edges) කරලා ආපහු GeoDataFrame කරනවා |
| **Geocoding** | `geopy`, `geopandas.tools.geocode` | Address එකක් coordinates (Lat/Lon) වලට හරවනවා (සහ ආපස්සට Reverse geocoding) | Address list එකකින් Point GeoDataFrame එකක් හදනවා (usage limits රකින්න ඕන!) |
| **Spatial statistics** | `pysal` family (`libpysal`, `esda`, `spreg`, `mapclassify`) | Spatial weights, autocorrelation (Moran's I), hotspots (Getis-Ord), spatial regression | Polygons වලින් spatial weights (`Queen.from_dataframe(gdf)`) හදනවා |
| **Spatial databases** | `PostGIS`, `SpatiaLite` | ලොකු spatial datasets SQL පාවිච්චි කරලා store කරලා query කරනවා | `gpd.read_postgis()`, `gdf.to_postgis()` |
| **Cloud-native formats** | `GeoParquet`, `FlatGeobuf`, `PMTiles`, `COG` | Cloud එකේ (S3, GCS) තියෙන files download නොකරම වේගයෙන් අවශ්‍ය කොටස විතරක් කියවනවා | `gpd.read_parquet()` එක්ක `bbox` හෝ `filters` දාලා කියවනවා |
| **Web maps** | `folium`, `leafmap`, `ipyleaflet`, `pydeck`, `lonboard` | Interactive maps සහ ලක්ෂ ගාණක් points පෙන්නන GPU-accelerated web maps | `gdf.explore()`, GeoJSON export, `lonboard` layers |
| **Geometry engine** | `shapely` 2.x | ඉතා වේගවත් vectorised geometry operations (C/GEOS backend) | GeoPandas ඇතුළෙත් array මට්ටමෙන් කෙලින්ම call කරන්න පුළුවන් |
| **Coordinate engine** | `pyproj` | CRS conversions සහ පෘථිවි වක්‍රතාවය සලකා බලන geodesic මිනුම් (`Geod`) | නිවැරදි geodesic length සහ area ගණන් හදන්න |
| **Satellite & Earth data** | `xarray`, `planetary-computer`, `earthengine-api` | Time-series satellite imagery (Sentinel-2, Landsat) | Polygon boundaries වලට අදාළව satellite values extract කරන්න |
| **Parallel geospatial** | `dask-geopandas`, `spatialpandas` | RAM එකට වඩා ලොකු spatial data කොටස් වලට කඩලා (partition) සමාන්තරව run කරනවා | Large data processing (Module 11 බලන්න) |
| **H3 & grid systems** | `h3`, `s2`, `mercantile` | Hexagonal (ෂඩාස්‍ර) grid indexes සහ web map tile indexes | මිලියන ගාණක් points ටික regular hex cells වලට `groupby` කරන්න |

---

## 3. Vector + Raster: `rasterio` සහ `zonal_stats`

Vector (polygons/points) සහ Raster (pixels/grids) එකට එකතු කරන ප්‍රධාන ක්‍රම දෙකක් තියෙනවා:
1. **Zonal Statistics (`rasterstats.zonal_stats`):** Polygon එකක් (උදා: දිස්ත්‍රික්කයක්) ඇතුළට වැටෙන raster pixels ඔක්කොගෙම `mean`, `max`, `min`, `sum` වගේ අගයන් හොයනවා.
2. **Point Sampling (`rasterio` `src.sample`):** හැම Point එකක්ම (උදා: කැෆේ හෝ ගෙයක්) පිහිටලා තියෙන හරියේ raster pixel අගය (උදා: Elevation / උස) ගන්නවා.

![Vector and Raster Zonal Stats and Point Sampling](images/gpd_10_vector_raster_zonal_stats.png)

---

## 4. සම්පූර්ණ Code උදාහරණය (OSMnx + Rasterio + Zonal Stats + Geocoding)

පහළ තියෙන්නේ Course එකේ තියෙන සම්පූර්ණ ecosystem workflow එකයි:

```python
import geopandas as gpd
import osmnx as ox
import rasterio
from rasterstats import zonal_stats
from geopandas.tools import geocode

# 1. OpenStreetMap (OSM) වලින් කෙලින්ම GeoDataFrame එකක් ගන්නවා
cafes = ox.features_from_place('Colombo, Sri Lanka', tags={'amenity': 'cafe'})
buildings = ox.features_from_place('Fort, Colombo', tags={'building': True})
print(f"Found {len(cafes)} cafes and {len(buildings)} buildings")

# 2. DEM Raster එකකින් Points තියෙන තැන්වල Elevation (උස) sample කරනවා
points = gpd.read_file('sample_points.gpkg')
with rasterio.open('dem.tif') as src:
    # ඉතා වැදගත්: points ටික raster එකේ CRS එකටම හරවන්න!
    coords = [(p.x, p.y) for p in points.to_crs(src.crs).geometry]
    points['elev'] = [v[0] for v in src.sample(coords)]

print(points[['name', 'elev']].head())

# 3. Rainfall Raster එකෙන් හැම දිස්ත්‍රික්කයකම සාමාන්‍ය සහ උපරිම වර්ෂාපතනය හොයනවා
districts = gpd.read_file('districts.gpkg')
stats = zonal_stats(districts, 'rainfall.tif', stats=['mean', 'max'], geojson_out=False)
districts['rain_mean'] = [s['mean'] for s in stats]
districts['rain_max'] = [s['max'] for s in stats]
print(districts[['district', 'rain_mean', 'rain_max']].head())

# 4. ලිපින (Addresses) coordinates වලට හරවනවා (Geocoding)
places = geocode(
    ['Galle Fort, Sri Lanka', 'Temple of the Tooth, Kandy'],
    provider='nominatim',
    user_agent='my-geo-course'
)
print(places[['address', 'geometry']])
```

### 🔍 පේළියෙන් පේළිය පැහැදිලි කිරීම (Line-by-Line Explanation):
- `ox.features_from_place('Colombo, Sri Lanka', tags={'amenity': 'cafe'})`: OpenStreetMap database එකෙන් කොළඹ ඇතුළේ `amenity=cafe` කියලා tag කරලා තියෙන හැම තැනක්ම GeoDataFrame එකක් විදිහට download කරනවා.
- `with rasterio.open('dem.tif') as src:`: GeoTIFF raster file එක open කරනවා. `with` පාවිච්චි කළාම වැඩේ ඉවර වුණ ගමන් file එක auto close වෙනවා.
- `points.to_crs(src.crs)`: Points වල CRS එක raster file එකේ CRS එකට (`src.crs`) සමාන කරනවා. මේක නොකළොත් වැරදි තැනකින් values ගන්නේ!
- `src.sample(coords)`: හැම `(x, y)` coordinate එකක් යටම තියෙන pixel අගය generator එකක් විදිහට දෙනවා. `v[0]` කියන්නේ Band 1 (elevation) අගය.
- `zonal_stats(districts, 'rainfall.tif', stats=['mean', 'max'], geojson_out=False)`: හැම district polygon එකක් ඇතුළෙම තියෙන rainfall pixels වල `mean` සහ `max` හදලා dictionary list එකක් දෙනවා.
- `geocode(..., provider='nominatim', user_agent='my-geo-course')`: Nominatim (OSM geocoder) පාවිච්චි කරලා නමක්/ලිපිනයක් `Point(lon, lat)` බවට හරවනවා. `user_agent` එක අනිවාර්යයෙන් ඔයාගේ app එකේ නමක් දෙන්න ඕන.

### 📤 බලාපොරොත්තු වෙන Output එක (Expected Output):
```text
Found 184 cafes and 612 buildings

          name   elev
0     Site_001   12.4
1     Site_002  145.8
2     Site_003  412.0

   district  rain_mean  rain_max
0   Colombo     192.4     248.0
1     Kandy     168.7     235.0
2     Galle     214.2     261.0

                                             address                   geometry
0  Galle Fort, Galle, Southern Province, Sri Lanka  POINT (80.21700 6.02670)
1  Sri Dalada Maligawa, Kandy, Central Province...  POINT (80.64130 7.29360)
```

---

## 5. Spatial Statistics (`PySAL` / `esda`), `PostGIS`, සහ `H3` Hex Grids

### A. Spatial Autocorrelation — Moran's I (`libpysal` + `esda`)
"අසල්වැසි දිස්ත්‍රික්ක වල අගයන් එකිනෙකට සමානද? (Cluster වෙලාද?)" කියලා සංඛ්‍යානමය වශයෙන් (statistically) බලන්න PySAL පාවිච්චි කරනවා:

```python
from libpysal.weights import Queen
from esda.moran import Moran

# 1. මායිම් ගෑවෙන අසල්වැසි polygons හඳුනාගන්නවා (Queen contiguity)
w = Queen.from_dataframe(districts, use_index=False)
w.transform = 'r'  # Row-standardize කරනවා

# 2. Disease rate හෝ poverty rate වගේ column එකකට Moran's I හදනවා
mi = Moran(districts['rate'], w)
print(f"Moran's I = {mi.I:.3f}, p-value = {mi.p_sim:.4f}")
```
- `mi.I > 0` සහ `p_sim < 0.05` නම්, ඒ දත්ත **statistically significant විදිහට cluster වෙලා** තියෙනවා!

### B. Spatial Database (`PostGIS`)
ලොකු datasets PostgreSQL + PostGIS වල store කරලා, SQL query එකකින් අවශ්‍ය කොටස විතරක් GeoPandas වලට ගන්න පුළුවන්:
```python
from sqlalchemy import create_engine

engine = create_engine('postgresql://user:pass@localhost:5432/gisdb')

# PostGIS එකෙන් කියවනවා
sql = "SELECT id, name, geom FROM buildings WHERE district = 'Colombo'"
bldg = gpd.read_postgis(sql, con=engine, geom_col='geom')

# ආපහු PostGIS එකේ table එකක් විදිහට save කරනවා
bldg.to_postgis('colombo_buildings', con=engine, if_exists='replace')
```

### C. `H3` Hexagonal Grid & `pyproj.Geod`
- **`h3`:** මිලියන ගාණක් GPS points ටික එකම ප්‍රමාණයේ ෂඩාස්‍ර (hexagons) වලට දාලා count කරන්න සුපිරියි (Uber හදපු library එක).
- **`pyproj.Geod`:** CRS එක projected කරන්නේ නැතුවම, WGS84 ගෝලය මත නිවැරදි geodesic දුර (`line_length`) සහ වර්ගඵලය (`geometry_area_perimeter`) මනින්න පුළුවන්.

---

## 6. Ethics සහ Terms of Service (ආචාරධර්ම සහ නීති)

> [!WARNING]
> **⚠️ සාමාන්‍යයෙන් වෙන වැරදි & නීති කඩවීම් (Common Pitfalls & Ethics):**
> 1. **Geocoder Rate Limits කඩන එක:** Nominatim වගේ free geocoding services වලට තත්පරයට request 1කට වඩා යවන්න එපා! ලොකු list එකක් තියෙනවා නම් `geopy.extra.rate_limiter.RateLimiter` පාවිච්චි කරලා තත්පර 1-2ක් pause දාන්න. නැත්නම් ඔයාගේ IP එක block වෙයි.
> 2. **CRS නොගැලපීම (Vector vs Raster):** `zonal_stats` හෝ `src.sample()` කරද්දී polygon/point වල CRS එක raster එකේ CRS එකට (`src.crs`) හරවන්න අමතක කළොත් `None` හෝ සම්පූර්ණයෙන් වැරදි අගයන් ලැබෙනවා.
> 3. **Attribution (මූලාශ්‍ර සඳහන් කිරීම) අමතක කිරීම:** OpenStreetMap data පාවිච්චි කරන හැම map එකකම `© OpenStreetMap contributors` කියලා credit දෙන්න ඕන.
> 4. **පෞද්ගලිකත්වය (Privacy):** රෝගීන්ගේ ගෙවල්, පුද්ගලික ලිපින වගේ precise personal locations කවදාවත් ප්‍රසිද්ධ map වල point විදිහට දාන්න එපා! ඒවා දිස්ත්‍රික්ක හෝ H3 grid cells වලට aggregate කරලා පෙන්නන්න.

---

## 🧠 මතක තබා ගැනීමට පහසු ක්‍රම (Memory Hacks)

1. **"R-O-G-P" Ecosystem Rule:**
   - **R**asterio / **R**asterstats → Grids (DEM, Rainfall, Satellite)
   - **O**SMnx → OpenStreetMap (Roads, Buildings, Cafes)
   - **G**eopy / **G**eocode → Addresses ↔ Coordinates
   - **P**ySAL / **P**ostGIS → Spatial Stats & Spatial Databases
2. **Point vs Polygon with Rasters:**
   - **Point** එකකට pixel අගය ගන්න → `src.sample(coords)` (හරියට ඇල්පෙනෙත්තක් ගහලා අගය ගන්නවා වගේ)
   - **Polygon** එකක් ඇතුළේ pixels වල summary ගන්න → `zonal_stats()` (හරියට කූඩෙකට වැටෙන pixels ඔක්කොගෙම සාමාන්‍යය ගන්නවා වගේ)
3. **OSMnx Magic Line:** ඕනම නගරයක data ගන්න මතක තියාගන්න → `ox.features_from_place("City, Country", tags={...})`.

---

## 📝 ප්‍රායෝගික අභ්‍යාස (Practical Exercises)

1. OpenStreetMap (`osmnx`) පාවිච්චි කරලා ඔයාගේ නගරයේ තියෙන cafes හෝ schools download කරලා, දිස්ත්‍රික්ක map එකක් උඩ plot කරන්න.
2. Raster file එකක් (`dem.tif` හෝ `rainfall.tif`) සහ `zonal_stats` පාවිච්චි කරලා හැම දිස්ත්‍රික්කයකම සාමාන්‍ය elevation හෝ rainfall ගණන් හදන්න.
3. ලිපින 10ක් geocode කරලා, map එකේ පෙන්නලා ඒවා හරිද බලන්න; geocode නොවුණ (failed / `None`) ලිපින handle කරන්න.
4. `esda` සහ Queen-contiguity spatial weight එකක් පාවිච්චි කරලා දිස්ත්‍රික්ක අනුව rate column එකක Moran's I හොයන්න.

<details>
<summary>✅ පිළිතුර බලන්න (Click to see Solution)</summary>

```python
import time
import geopandas as gpd
import osmnx as ox
import matplotlib.pyplot as plt
from rasterstats import zonal_stats
from geopy.geocoders import Nominatim
from geopy.extra.rate_limiter import RateLimiter
from shapely.geometry import Point
from libpysal.weights import Queen
from esda.moran import Moran

# --- Exercise 1: OSMnx cafes/schools over district map ---
districts = gpd.read_file('districts.gpkg')
schools = ox.features_from_place('Kandy, Sri Lanka', tags={'amenity': 'school'})
# OSMnx වල සමහර schools polygons විදිහට එන නිසා points බවට හරවමු
schools_pts = schools.copy()
schools_pts['geometry'] = schools_pts.to_crs(32644).centroid.to_crs(districts.crs)

ax = districts[districts['district'] == 'Kandy'].plot(color='#F1F5F9', edgecolor='black')
schools_pts.plot(ax=ax, color='red', markersize=18, label='Schools')
plt.legend()
plt.title('Schools in Kandy (OSM)')
plt.show()

# --- Exercise 2: Mean rainfall per district using zonal_stats ---
import rasterio
with rasterio.open('rainfall.tif') as src:
    districts_r = districts.to_crs(src.crs)

stats = zonal_stats(districts_r, 'rainfall.tif', stats=['mean', 'max', 'count'])
districts['rain_mean'] = [s['mean'] for s in stats]
print(districts[['district', 'rain_mean']].head())

# --- Exercise 3: Geocode 10 addresses with rate limiting & failure handling ---
addresses = [
    'Galle Fort, Sri Lanka', 'Sigiriya, Sri Lanka', 'Anuradhapura, Sri Lanka',
    'Jaffna Fort, Sri Lanka', 'Nuwara Eliya, Sri Lanka', 'Ella, Sri Lanka',
    'Trincomalee, Sri Lanka', 'Polonnaruwa, Sri Lanka', 'Matara, Sri Lanka',
    'NonExistentFakePlaceXYZ12345'
]
geolocator = Nominatim(user_agent='geopandas-self-study-course')
safe_geocode = RateLimiter(geolocator.geocode, min_delay_seconds=1.2)

rows = []
for addr in addresses:
    loc = safe_geocode(addr)
    if loc is not None:
        rows.append({'input_addr': addr, 'status': 'OK',
                     'geometry': Point(loc.longitude, loc.latitude)})
    else:
        rows.append({'input_addr': addr, 'status': 'FAILED', 'geometry': None})

geo_df = gpd.GeoDataFrame(rows, crs='EPSG:4326')
print("Failed count:", (geo_df['status'] == 'FAILED').sum())
valid_pts = geo_df.dropna(subset=['geometry'])
valid_pts.explore()  # Visually check on interactive map!

# --- Exercise 4: Moran's I with Queen contiguity ---
w = Queen.from_dataframe(districts, use_index=False)
w.transform = 'r'
mi = Moran(districts['rate'], w)
print(f"Moran's I: {mi.I:.4f}, z-score: {mi.z_sim:.2f}, p-value: {mi.p_sim:.4f}")
```

</details>

---

[🏠 පාඨමාලා මුල් පිටුව (Course Index)](README.md) | [⬅️ පෙර පාඩම: 09 Visualisation & Interactive Maps](09_Visualisation_and_Interactive_Maps.md) | [ඊළඟ පාඩම: 11 Performance & Scaling ➡️](11_Performance_and_Scaling.md)
