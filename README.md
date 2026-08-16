# Tanganyika 1950 Shapefiles

Historical **Tanganyika province and district boundaries for 1950**, prepared for direct use in QGIS, ArcGIS, GeoPandas, R `sf`, and other GIS software.

This repository builds GIS layers from the digitized historical administrative boundary dataset held by Princeton University Library / the NYU Spatial Data Repository. The underlying historical map is a British colonial-era administrative map of Tanganyika with province and district boundaries.

> **Important:** this repository republishes derived GIS outputs and automation around a historical source dataset. Check and preserve the source dataset's attribution and reuse conditions when redistributing derived data.

## What is included

The build produces two main historical layers:

- **Tanganyika Provinces, 1950** — province polygons dissolved from the historical district source.
- **Tanganyika Districts, 1950** — the original internal administrative district polygons.

The district layer can be drawn with thin boundaries and the province layer with thick boundaries to reproduce the hierarchy visible on historical Tanganyika maps.

## Output files

After the GitHub Actions workflow completes, the repository contains:

```text
data/tanganyika_1950/
├── Tanganyika_Provinces_1950.shp
├── Tanganyika_Provinces_1950.shx
├── Tanganyika_Provinces_1950.dbf
├── Tanganyika_Provinces_1950.prj
├── Tanganyika_Provinces_1950.cpg
├── Tanganyika_Provinces_1950.geojson
├── Tanganyika_Provinces_1950.gpkg
├── Tanganyika_Districts_1950.shp
├── Tanganyika_Districts_1950.shx
├── Tanganyika_Districts_1950.dbf
├── Tanganyika_Districts_1950.prj
├── Tanganyika_Districts_1950.cpg
├── Tanganyika_Districts_1950.geojson
├── Tanganyika_Districts_1950.gpkg
├── Tanganyika_1950_Province_District_Crosswalk.csv
└── summary.json

dist/
├── Tanganyika_Provinces_1950_Shapefile.zip
└── Tanganyika_Districts_1950_Shapefile.zip

docs/
└── Tanganyika_1950_preview.png
```

## Quick download

Open the repository's **Releases** page and download the assets under the release tag:

```text
tanganyika-1950
```

The release includes ready-to-use Shapefile ZIP packages, GeoJSON, GeoPackage, the province/district crosswalk CSV, summary metadata, and a preview image.

## Coordinate reference system

Generated layers are standardized to:

```text
EPSG:4326 — WGS 84
```

This makes the outputs easy to combine with modern Tanzania basemaps and other global GIS datasets.

## Using in QGIS

### Provinces

1. Download `Tanganyika_Provinces_1950_Shapefile.zip` from Releases.
2. Extract the ZIP.
3. Open QGIS.
4. Choose **Layer → Add Layer → Add Vector Layer**.
5. Select `Tanganyika_Provinces_1950.shp`.
6. Style the boundary with a relatively thick line.
7. Label using the `PROVINCE` field.

### Districts

1. Download `Tanganyika_Districts_1950_Shapefile.zip`.
2. Extract it.
3. Add `Tanganyika_Districts_1950.shp` to QGIS.
4. Use a thin boundary line.
5. Label using the `DISTRICT` field.

To reproduce the historical map appearance, place the **district layer below the province layer** so the province boundaries remain visually dominant.

## Attribute fields

The generated district layer contains:

```text
YEAR
TERRITORY
PROVINCE
DISTRICT
geometry
```

The province layer contains:

```text
YEAR
TERRITORY
PROVINCE
geometry
```

## Python / GeoPandas

```python
import geopandas as gpd

provinces = gpd.read_file("data/tanganyika_1950/Tanganyika_Provinces_1950.gpkg")
districts = gpd.read_file("data/tanganyika_1950/Tanganyika_Districts_1950.gpkg")

print(provinces[["YEAR", "TERRITORY", "PROVINCE"]])
print(districts[["PROVINCE", "DISTRICT"]])

ax = districts.boundary.plot(linewidth=0.4)
provinces.boundary.plot(ax=ax, linewidth=1.5)
```

## R / sf

```r
library(sf)

provinces <- st_read("data/tanganyika_1950/Tanganyika_Provinces_1950.gpkg")
districts <- st_read("data/tanganyika_1950/Tanganyika_Districts_1950.gpkg")

plot(st_geometry(districts), border = "grey")
plot(st_geometry(provinces), add = TRUE, lwd = 2)
```

## Build locally

Install the Python dependencies:

```bash
python -m pip install -r requirements.txt
```

Place the extracted historical source shapefile somewhere under `source_1950/`, then run:

```bash
python scripts/build_tanganyika_1950.py \
  --source source_1950 \
  --output data/tanganyika_1950 \
  --dist dist \
  --docs docs
```

The script automatically locates the source `.shp`, validates the historical province and district fields, repairs invalid geometry where possible, converts the data to EPSG:4326, generates all formats, makes the crosswalk, packages Shapefiles, and renders a preview map.

## GitHub Actions automation

The workflow is located at:

```text
.github/workflows/build-tanganyika-1950.yml
```

It automatically:

1. Checks out this repository.
2. Sets up Python.
3. Installs the GIS dependencies.
4. Downloads the historical Tanganyika source dataset.
5. Generates province and district layers.
6. Validates the required outputs.
7. Commits generated GIS files back to `main` when they change.
8. Creates or updates the `tanganyika-1950` GitHub Release.
9. Uploads all generated data as a workflow artifact.

The workflow can also be started manually from **Actions → Build Tanganyika 1950 Boundaries → Run workflow**.

## Source and historical basis

The historical polygons originate from a digitized administrative map of Tanganyika held by Princeton University Library / the NYU Spatial Data Repository. The dataset contains province and district information derived from a British Colonial Office map and is substantially more suitable for reconstructing the map shown in historical atlases than attempting to reverse modern Tanzanian regional boundaries.

## Repository structure

```text
Tanganyika-1950-Shapefiles/
├── .github/
│   └── workflows/
│       └── build-tanganyika-1950.yml
├── scripts/
│   └── build_tanganyika_1950.py
├── requirements.txt
├── data/                     # generated by GitHub Actions
├── dist/                     # generated release ZIPs
├── docs/                     # generated preview
└── README.md
```

## Historical GIS note

Administrative boundaries changed repeatedly during the colonial period. A layer identified as **1950** should therefore be treated as a snapshot of the administrative structure represented by that source map, not as a universal boundary set for every year of Tanganyika's existence.
