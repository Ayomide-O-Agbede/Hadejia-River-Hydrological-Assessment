# Data Sources

This document lists the main datasets used in the **Hadejia River Watershed Hydrological Assessment**, their sources, purpose and important limitations.

## 1. Data Summary

| Dataset                    | Source                   | Main Use                                                   | Type        |
| -------------------------- | ------------------------ | ---------------------------------------------------------- | ----------- |
| SRTM 30 m DEM              | USGS EarthExplorer       | Elevation, terrain, watershed delineation                  | Raster      |
| HydroBASINS Africa Level 6 | HydroSHEDS / HydroBASINS | Preliminary watershed reference and DEM acquisition extent | Vector      |
| Wudil daily streamflow     | GRDC                     | Observed streamflow analysis                               | Time series |
| ESA WorldCover 2021        | ESA                      | Land-cover analysis                                        | Raster      |
| QSWAT watershed/subbasins  | Derived                  | Final watershed and subbasin analysis                      | Vector      |
| QSWAT drainage network     | Derived                  | Drainage and morphometric analysis                         | Vector      |
| SRTM-derived slope         | Derived in QGIS          | Slope analysis and mapping                                 | Raster      |

## 2. SRTM 30 m DEM

### Source

**USGS EarthExplorer**

### Purpose

The SRTM DEM was the main elevation dataset for:

* Watershed delineation
* Subbasin delineation
* Elevation analysis
* Relief calculation
* Slope analysis
* Drainage-network extraction
* Terrain mapping

### Processing

The downloaded SRTM tiles were mosaicked, clipped to the working area and reprojected to:

**WGS 84 / UTM Zone 32N — EPSG:32632**

The main processed DEM was:

```text
Hadejia_SRTM_DEM_UTM32N.tif
```

The final watershed elevation statistics were:

* Minimum: **334.07 m**
* Maximum: **1,568.76 m**
* Mean: **552.53 m**
* Relief: **1,234.69 m**

### Limitation

The DEM has a spatial resolution of approximately 30 m. Small-scale terrain features may therefore not be fully represented.

## 3. HydroBASINS Africa Level 6

### Source

**HydroSHEDS / HydroBASINS**

### Purpose

HydroBASINS was used as an initial spatial reference for identifying the drainage basin associated with the Wudil gauge and for defining a suitable area for DEM acquisition.

Relevant feature:

* `HYBAS_ID = 1060708880`
* `NEXT_DOWN = 1060700890`
* Upstream area ≈ **24,069.7 km²**

### Important Processing Decision

The HydroBASINS boundary was **not used as the final watershed boundary**.

The final watershed was delineated independently using QSWAT and the SRTM DEM.

The original HydroBASINS shapefile was converted to GeoPackage format during the DEM acquisition workflow.

## 4. Wudil Daily Streamflow

### Source

**Global Runoff Data Centre (GRDC)**

### Station

**Wudil — GRDC Station 1837410**

Approximate coordinates:

* Latitude: **11.797° N**
* Longitude: **8.834° E**

### Data Type

Daily observed discharge in:

**m³/s**

The original GRDC `.day` file contains daily observations and uses `-9999` as the missing-value code.

### Selected Study Period

**1 January 1976 – 31 December 1990**

After processing:

* **5,479 daily observations**
* No missing discharge values within the selected period

Key statistics:

* Mean: **35.32 m³/s**
* Median: **14.00 m³/s**
* Minimum: **0 m³/s**
* Maximum: **704.68 m³/s**

### Derived Analyses

The streamflow dataset was used to produce:

* Daily hydrograph
* Monthly mean discharge
* Annual mean discharge
* Flow Duration Curve
* Q10, Q50 and Q90
* High- and low-flow thresholds
* Recorded zero-discharge days
* Monthly climatology
* Annual coefficient of variation
* Annual linear trend

### Limitation

The analysis is based on a single gauge and therefore represents observed streamflow at Wudil rather than every location in the watershed.

The recorded zero-discharge values were retained but were not independently field-validated.

GRDC data should also be treated according to the provider's data-use and redistribution conditions.

## 5. ESA WorldCover 2021

### Source

**European Space Agency (ESA) WorldCover**

### Purpose

ESA WorldCover 2021 was used to describe the spatial distribution of land cover within the final watershed.

The original classes include:

* Tree cover
* Shrubland
* Grassland
* Cropland
* Built-up
* Bare/sparse vegetation
* Permanent water bodies
* Herbaceous wetland

### Processing

The raster was prepared and clipped to the final watershed boundary.

The watershed-specific output was used for the final land-cover map.

### Limitation

The dataset represents **2021**, while the streamflow analysis covers **1976–1990**. The land-cover layer should therefore be treated as a spatial representation of the watershed rather than as a direct representation of land cover during the streamflow observation period.

## 6. QSWAT-Derived Watershed and Subbasins

These are derived datasets produced during watershed delineation.

### Inputs

* Processed 30 m SRTM DEM
* Wudil gauge location
* Outlet/snap location
* QSWAT stream-definition settings

### Main Settings

* Stream threshold: approximately **1,088,390 cells**
* Approximate threshold area: **1,008 km²**
* Snap threshold: **300 m**

### Outputs

* Final watershed boundary
* **10 subbasins**
* Snapped outlet
* QSWAT drainage network

The final watershed area was:

**22,619.66 km²**

The watershed geometry was corrected using QGIS **Fix Geometries** before subsequent spatial analysis.

## 7. QSWAT-Derived Drainage Network

The drainage network was generated during QSWAT watershed delineation.

Final characteristics:

* **10 mapped reaches**
* **791.96 km** total mapped length
* Maximum mapped stream order: **3rd order**

The network was used to calculate:

* Total mapped drainage length
* Stream order
* Mapped drainage density
* Mapped reach frequency

### Limitation

The network represents the QSWAT-derived drainage structure and does not necessarily include every natural or artificial drainage feature in the watershed.

## 8. Derived Slope Raster

Slope was calculated from the projected 30 m SRTM DEM using QGIS.

The resulting slope surface was clipped to the final watershed.

Output:

```text
Hadejia_Slope_Watershed.tif
```

Final watershed-specific values:

* Area-weighted mean slope: **2.16°**
* Maximum slope: **47.30°**

The final map used:

* 0–5° — Low Slope
* 5–10° — Moderate Slope
* > 10° — Steep Slope

These classes were selected for this project and are not universal slope standards.

## 9. Data Lineage

The main spatial data lineage was:

```text
HydroBASINS
      ↓
Preliminary spatial reference
      ↓
DEM acquisition extent
      ↓
SRTM DEM
      ↓
Mosaic / Clip / Reproject
      ↓
QSWAT
      ↓
Final watershed + subbasins + drainage network
      ↓
Terrain / morphometric / slope analysis
```

The streamflow data lineage was:

```text
GRDC Wudil Station
      ↓
Daily discharge
      ↓
Missing-value processing
      ↓
1976–1990 filtering
      ↓
Quality control
      ↓
Hydrological and statistical analysis
```

The land-cover lineage was:

```text
ESA WorldCover 2021
      ↓
Preparation
      ↓
Clip to final watershed
      ↓
Land-cover map
```

## 10. External and Derived Data

### External datasets

The main external datasets were:

1. **SRTM 30 m DEM**
2. **HydroBASINS Africa Level 6**
3. **GRDC Wudil daily streamflow**
4. **ESA WorldCover 2021**

### Derived datasets

The main derived outputs were:

* Final QSWAT watershed
* QSWAT subbasins
* QSWAT drainage network
* Projected/processed DEM
* Watershed-specific slope raster
* Processed streamflow dataset
* Streamflow figures and summary statistics

## 11. Data Quality Notes

The following points are important when interpreting the datasets:

* HydroBASINS was used as a preliminary reference, not as the final watershed boundary.
* The final watershed was generated using QSWAT from the SRTM DEM.
* The drainage network is QSWAT-derived.
* Drainage density is therefore reported as **mapped drainage density**.
* Terrain statistics depend on the 30 m SRTM DEM and final watershed boundary.
* The slope results depend on the DEM and the watershed-specific clipped raster.
* ESA WorldCover represents **2021** and does not match the 1976–1990 streamflow period.
* Streamflow represents one gauge location.
* Recorded zero-discharge values were not independently validated.
* No gap filling or rainfall-runoff simulation was applied to the streamflow record.

## 12. Data Availability

The repository contains the processed and derived outputs needed to understand and reproduce the analysis where redistribution of the original datasets is permitted.

External datasets remain subject to the terms and conditions of their respective providers, particularly the GRDC streamflow data.

The repository therefore documents the source, processing and role of each dataset without assuming redistribution rights for externally sourced data.
