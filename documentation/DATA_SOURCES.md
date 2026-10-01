# Data Sources

This document describes the datasets used in the **Hadejia River Watershed Hydrological Assessment**, including their sources, purpose, processing, and role in the final analysis.

The project combines externally sourced datasets with GIS- and QSWAT-derived datasets produced during the analysis.

---

## 1. Data Summary

| Dataset                    | Source                           | Main Use                                                     | Data Type   |
| -------------------------- | -------------------------------- | ------------------------------------------------------------ | ----------- |
| 30 m SRTM DEM              | USGS EarthExplorer               | Elevation, terrain analysis, watershed delineation           | Raster      |
| HydroBASINS Africa Level 6 | HydroSHEDS / HydroBASINS         | Preliminary watershed reference and DEM acquisition envelope | Vector      |
| Wudil daily streamflow     | Global Runoff Data Centre (GRDC) | Observed streamflow analysis                                 | Time series |
| ESA WorldCover 2021        | European Space Agency            | Land-cover analysis                                          | Raster      |
| QSWAT watershed            | Derived using QSWAT              | Final watershed and subbasin delineation                     | Vector      |
| QSWAT drainage network     | Derived using QSWAT              | Drainage-network and morphometric analysis                   | Vector      |
| Processed slope raster     | Derived from SRTM DEM in QGIS    | Slope analysis and mapping                                   | Raster      |

---

# 2. SRTM Digital Elevation Model

### Source

**USGS EarthExplorer**

### Dataset

**Shuttle Radar Topography Mission (SRTM) 30 m DEM**

### Purpose

The SRTM DEM was the primary elevation dataset for:

* Watershed delineation
* Subbasin delineation
* Elevation analysis
* Relief calculation
* Slope derivation
* Drainage-network extraction
* Terrain visualization

### Acquisition

The geographic area required for the study was first identified using the HydroBASINS Africa Level 6 basin containing the Wudil gauge/Hadejia system.

The required SRTM tiles were downloaded from USGS EarthExplorer and combined into a single DEM mosaic.

The HydroBASINS reference was used as a **generous acquisition envelope** so that the eventual QSWAT watershed would not be cut off by the DEM coverage.

### Processing

The DEM was processed in QGIS through the following stages:

1. Individual SRTM tiles were loaded into QGIS.
2. The tiles were mosaicked into a continuous raster.
3. The resulting mosaic was clipped to the working study extent.
4. The DEM was reprojected from geographic coordinates to **WGS 84 / UTM Zone 32N (EPSG:32632)**.
5. The reprojected DEM was used as the working elevation surface for QSWAT and terrain analysis.

### Main Project Files

```text
Hadejia_SRTM_DEM_Mosaic.tif
Hadejia_SRTM_DEM_Clipped.tif
Hadejia_SRTM_DEM_UTM32N.tif
```

### Final Watershed Analysis

The final watershed-specific elevation statistics were calculated using the QSWAT-derived watershed geometry and the processed SRTM DEM.

Results:

* Minimum elevation: **334.07 m**
* Maximum elevation: **1,568.76 m**
* Mean elevation: **552.53 m**
* Relief: **1,234.69 m**

### Limitation

The DEM represents elevation at approximately 30 m spatial resolution. Small-scale terrain features that occur below the DEM resolution may therefore not be represented.

---

# 3. HydroBASINS Africa Level 6

### Source

**HydroSHEDS / HydroBASINS**

### Dataset

**HydroBASINS Africa Level 6**

### Purpose

HydroBASINS was used as a **preliminary watershed reference**, rather than as the final project watershed boundary.

The Level 6 basin containing the Wudil gauge was identified and used to:

* Provide an initial geographic reference for the Hadejia/Wudil system.
* Help determine the area required for DEM acquisition.
* Provide a generous spatial envelope around the eventual watershed.

### Identified Feature

The relevant HydroBASINS feature had:

* `HYBAS_ID = 1060708880`
* `NEXT_DOWN = 1060700890`
* `UP_AREA ≈ 24,069.7 km²`

### Processing

The original Africa Level 6 shapefile was loaded into QGIS.

The HydroBASINS Level 6 shapefile was converted to GeoPackage to satisfy the USGS spatial-input requirement encountered during DEM acquisition.

### Important Data-Lineage Note

HydroBASINS was **not used as the final Hadejia watershed boundary**.

The final watershed boundary used throughout the project was generated independently using **QSWAT** from the processed SRTM DEM and the Wudil outlet location.

Therefore:

```text
HydroBASINS
      ↓
Preliminary geographic reference
      ↓
DEM acquisition envelope
      ↓
SRTM DEM
      ↓
QSWAT watershed delineation
      ↓
Final Hadejia watershed
```

This distinction is important because the HydroBASINS upstream area and the final QSWAT watershed area are not identical.

---

# 4. Wudil Streamflow Dataset

### Source

**Global Runoff Data Centre (GRDC)**

### Station

**Wudil**

### GRDC Station ID

**1837410**

### Coordinates

* Latitude: **11.797° N**
* Longitude: **8.834° E**

### Data Type

Daily observed streamflow discharge.

### Original Record

The GRDC dataset contains daily observations extending beyond the period selected for this project.

The original data file contains:

* Daily measurements
* One measurement per day
* Discharge values in **m³/s**
* Missing-value code: **−9999.000**

### Project Study Period

The analysis was restricted to:

**1 January 1976 – 31 December 1990**

This produced:

* **5,479 daily observations**
* **0 missing discharge values after quality control**

### Processing

The original GRDC `.day` file was read and processed in Python.

The processing workflow included:

1. Reading the fixed-width/space-separated daily observations.
2. Replacing the GRDC missing-value code (`-9999.000`) with a null value.
3. Creating a date field from year, month, and day.
4. Filtering the observations to the 1976–1990 study period.
5. Checking the resulting dataset for missing discharge values.
6. Using the cleaned time series for subsequent hydrological analysis.

### Main Input File

```text
Hadejia.day
```

### Analyses Derived from the Dataset

The streamflow dataset was used to calculate:

* Daily streamflow hydrograph
* Monthly mean discharge
* Annual mean discharge
* Flow Duration Curve
* Q10
* Q50
* Q90
* High-flow threshold
* Low-flow threshold
* High- and low-flow days
* Recorded zero-discharge days
* Monthly climatology
* Annual coefficient of variation
* Annual linear trend

### Key Dataset Statistics

| Parameter                    |           Value |
| ---------------------------- | --------------: |
| Mean discharge               |  **35.32 m³/s** |
| Median discharge             |  **14.00 m³/s** |
| Minimum discharge            |   **0.00 m³/s** |
| Maximum recorded discharge   | **704.68 m³/s** |
| Standard deviation           |  **58.09 m³/s** |
| Q10                          |   **95.9 m³/s** |
| Q50                          |   **14.0 m³/s** |
| Q90                          |    **1.9 m³/s** |
| Recorded zero-discharge days |          **60** |

### Limitation

The analysis describes the observed discharge record at a **single gauge station** and does not by itself represent streamflow conditions at every location within the watershed.

Recorded zero-discharge values were retained as reported in the source dataset and were not independently validated against field observations.

---

# 5. ESA WorldCover 2021

### Source

**European Space Agency (ESA) WorldCover**

### Dataset

**ESA WorldCover 2021**

### Purpose

WorldCover 2021 was used to provide a spatial representation of land-cover conditions within the final Hadejia watershed.

The land-cover analysis supports the broader watershed assessment by showing the spatial distribution of major land-cover classes.

### Land-Cover Classes Used

The WorldCover categorical classes represented in the project include:

| Class Value | Land-Cover Class         |
| ----------: | ------------------------ |
|          10 | Tree cover               |
|          20 | Shrubland                |
|          30 | Grassland                |
|          40 | Cropland                 |
|          50 | Built-up                 |
|          60 | Bare / sparse vegetation |
|          80 | Permanent water bodies   |
|          90 | Herbaceous wetland       |

### Processing

The WorldCover dataset was:

1. Loaded into QGIS.
2. Reprojected to the project working coordinate system where required.
3. Clipped to the final Hadejia watershed boundary.
4. Styled using categorical land-cover classes.
5. Used to produce the final land-cover map.

### Main Project File

```text
Hadejia_WorldCover_Watershed.tif
```

### Limitation

WorldCover represents land-cover classes for the **2021 reference year**. It therefore describes land cover for that period and should not be interpreted as a direct representation of land-cover conditions throughout the 1976–1990 streamflow analysis period.

---

# 6. QSWAT-Derived Watershed and Subbasins

### Source

**Derived from SRTM DEM using QSWAT**

### Purpose

The QSWAT output provided the **final watershed boundary** and the subdivision of the watershed into hydrologically connected subbasins.

### Delineation Inputs

The delineation used:

* Processed 30 m SRTM DEM
* Wudil gauge location
* Outlet/snap location
* QSWAT stream-definition settings

The stream threshold used during the delineation was approximately **1,088,390 cells**, corresponding to an area of approximately **1,008 km²** at the working DEM resolution.

A snap threshold of **300 m** was used to associate the gauge/outlet with the derived drainage network.

### Output

The QSWAT delineation produced:

* **10 subbasins**
* Watershed boundary
* Derived stream/reach network
* Snapped outlet location

### Final Fixed Geometry

The original watershed geometry contained invalid geometry that was corrected in QGIS using **Fix Geometries**.

The corrected geometry was stored as:

```text
Hadejia_Watershed_Fixed.gpkg
```

and

```text
Hadejia_Watershed_Fixed_Geometry.gpkg
```

The final watershed analysis used the corrected geometry.

### Watershed Area

The total watershed area calculated from the fixed geometry is:

**22,619.66 km²**

### Coordinate Reference System

**WGS 84 / UTM Zone 32N (EPSG:32632)**

---

# 7. QSWAT-Derived Drainage Network

### Source

**Derived using QSWAT from the processed SRTM DEM**

### Purpose

The QSWAT-derived drainage network was used for:

* Drainage-network mapping
* Reach-length calculation
* Mapped drainage density
* Stream-order analysis
* Mapped reach frequency

### Derived Network

The mapped network contains:

* **10 reaches**
* **791.96 km** total mapped reach length
* Maximum mapped stream order: **3rd order**

### Stream Order

The QSWAT-derived network contains:

| Stream Order | Number of Reaches |
| -----------: | ----------------: |
|    1st order |                 6 |
|    2nd order |                 2 |
|    3rd order |                 2 |

The stream-order values are the **QSWAT-derived `strmOrder` values** and were not independently recalculated using a separate manual Strahler-order procedure.

### Drainage Density

Mapped drainage density was calculated as:

$$
D_d=\frac{L}{A}
$$

where:

* \(D_d\) = mapped drainage density (km/km²)
* \(L\) = total mapped drainage length (km)
* \(A\) = watershed area (km²)

Using:

$$
D_d=\frac{791.96}{22619.66}
$$

gives:

**0.0350 km/km²**

### Limitation

Because the drainage network was derived using QSWAT and a selected DEM/stream threshold, the result represents the **mapped QSWAT-derived drainage network** rather than every natural or artificial drainage feature within the watershed.

---

# 8. Derived Slope Raster

### Source

**Derived from the 30 m SRTM DEM in QGIS**

### Purpose

The slope raster was used to examine terrain steepness across the watershed and produce the watershed-specific slope map.

### Processing

Slope was calculated from the reprojected SRTM DEM in QGIS.

The resulting slope raster was subsequently clipped to the final Hadejia watershed boundary.

### Main Watershed-Specific File

```text
Hadejia_Slope_Watershed.tif
```

### Watershed-Specific Range

The clipped watershed slope raster has a maximum slope of approximately:

**47.30°**

The area-weighted mean slope used in the integrated analysis was:

**2.16°**

### Classification

For cartographic and hydrological representation, the slope was classified into:

| Range     | Class          |
| --------- | -------------- |
| **0–5°**  | Low Slope      |
| **5–10°** | Moderate Slope |
| **>10°**  | Steep Slope    |

These classes were selected for this project to provide a clear representation of the terrain distribution and are not presented as universal hydrological thresholds.

---

# 9. Coordinate Reference System

The main spatial analysis was carried out using:

**WGS 84 / UTM Zone 32N**

**EPSG:32632**

The UTM projection was used for spatial calculations such as:

* Area
* Perimeter
* Distance
* Drainage length
* Drainage density
* Spatial overlays
* Terrain analysis

The original HydroBASINS and Wudil gauge datasets were initially available in geographic coordinates and were reprojected where required for the spatial analysis.

---

# 10. Data Lineage

The main data-processing lineage for the project can be summarized as follows:

```text
HydroBASINS Africa Level 6
        │
        └── Preliminary geographic reference
                    │
                    ▼
             DEM acquisition area
                    │
                    ▼
             USGS SRTM 30 m DEM
                    │
                    ▼
       Mosaic → Clip → Reproject
                    │
                    ▼
        QSWAT watershed delineation
                    │
          ┌─────────┴─────────┐
          ▼                   ▼
    Final watershed       Subbasins
          │                   │
          ├──────────┐        │
          ▼          ▼        ▼
       Terrain    Drainage   Morphometric
       analysis   network     analysis
          │          │
          ▼          ▼
       Slope      Reach length,
                  stream order,
                  drainage density
```

The hydrological time-series branch is separate:

```text
GRDC Wudil Gauge
Station 1837410
        │
        ▼
Daily observed discharge
        │
        ▼
Filter to 1976–1990
        │
        ▼
Quality control
        │
        ├── Daily hydrograph
        ├── Monthly mean
        ├── Annual mean
        ├── Flow Duration Curve
        ├── High/low-flow analysis
        ├── Monthly climatology
        ├── Annual CV
        └── Trend analysis
```

Land-cover analysis forms another independent branch:

```text
ESA WorldCover 2021
        │
        ▼
Reprojection / preparation
        │
        ▼
Clip to final watershed
        │
        ▼
Land-cover map
```

---

# 11. External Data vs. Derived Data

It is important to distinguish between datasets obtained from external providers and datasets generated during this project.

### External datasets

* USGS SRTM 30 m DEM
* HydroBASINS Africa Level 6
* GRDC Wudil daily streamflow
* ESA WorldCover 2021

### Derived project datasets

* Final QSWAT watershed
* QSWAT subbasins
* QSWAT drainage network
* Reprojected and processed DEM
* Watershed-specific slope raster
* Fixed watershed geometry
* Derived morphometric statistics
* Hydrological statistics and figures generated in Python

Derived datasets are products of the processing workflow documented in this repository and should be interpreted together with the processing parameters and source-data limitations.

---

# 12. Data Availability and Redistribution

The project uses data from several external providers with their own access, licensing, attribution, and redistribution conditions.

External datasets are therefore not automatically redistributed through this repository.

In particular, the original **GRDC daily streamflow dataset** should only be redistributed where the applicable GRDC data-use conditions permit it.

The repository documentation records the dataset identity, station information, study period, processing workflow, and derived results so that the analytical process remains transparent even when the original source files are not included.

Users reproducing the analysis should obtain the original datasets directly from their respective providers and follow the applicable terms of use.

---

# 13. Data Quality and Interpretation Notes

Several considerations should be kept in mind when interpreting the project datasets:

1. **HydroBASINS is a preliminary reference.** It was not used as the final watershed boundary.

2. **QSWAT defines the final watershed.** The final boundary and subbasins were generated from the SRTM DEM and Wudil outlet setup.

3. **The drainage network is QSWAT-derived.** Drainage density and reach frequency therefore describe the mapped network produced by the delineation settings.

4. **Terrain statistics are watershed-specific.** Elevation statistics used in the project were calculated over the final watershed rather than using the statistics of the larger DEM acquisition area.

5. **Slope values depend on the DEM and processing.** The final slope map uses the watershed-clipped slope raster.

6. **WorldCover represents 2021.** It should not be treated as a historical land-cover dataset for the 1976–1990 streamflow period.

7. **Streamflow represents one observation point.** The Wudil record provides observed discharge at the gauge and does not directly describe spatially distributed flow across the entire watershed.

8. **Recorded zero-discharge values are dataset observations.** They were not independently verified against field measurements.

---

## 14. Summary

The project integrates four main external data sources:

* **USGS SRTM 30 m DEM** for elevation and terrain-based watershed analysis.
* **HydroBASINS Africa Level 6** for preliminary basin reference and DEM acquisition planning.
* **GRDC Wudil Station 1837410** for observed daily streamflow analysis.
* **ESA WorldCover 2021** for land-cover assessment.

These datasets were processed in **QGIS/QSWAT and Python** to produce the final watershed boundary, subbasins, drainage network, terrain products, land-cover map, hydrological statistics, and visualizations presented in the project.

The complete processing workflow is described in:

* [`METHODOLOGY.md`](METHODOLOGY.md)
* [`PROCESSING_NOTES.md`](PROCESSING_NOTES.md)
