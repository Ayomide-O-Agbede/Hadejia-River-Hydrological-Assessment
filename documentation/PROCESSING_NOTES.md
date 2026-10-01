# Processing Notes

## 1. Purpose

This document records the practical processing decisions, workflow adjustments, quality-control checks, and technical issues encountered during the development of the **Hadejia River Watershed Hydrological Assessment**.

It complements the main methodology by documenting **how the datasets and outputs were prepared**, including decisions made during GIS processing, watershed delineation, raster preparation, map production, and Python-based streamflow analysis.

---

## 2. Project Workspace

The project was developed using a working directory containing separate folders for GIS processing, SWAT/QSWAT work, data, outputs, maps, figures, documentation, and the Python notebook.

The final GitHub repository was organised as:

```text
Hadejia-River-Hydrological-Assessment/
│
├── README.md
├── notebook/
│   └── Hadejia_Streamflow_Hydrological_Analysis.ipynb
├── maps/
│   ├── Map_1_Watershed_Overview.jpg
│   ├── Map_2_Subbasins_Drainage_Network.jpg
│   ├── Map_3_Slope.jpg
│   └── Map_4_Land_Cover.jpg
├── figures/
│   ├── Figure_1_Daily_Streamflow.png
│   ├── Figure_2_Monthly_Mean_Streamflow.png
│   ├── Figure_3_Annual_Mean_Streamflow.png
│   ├── Figure_4_Flow_Duration_Curve.png
│   ├── Figure_5_Monthly_Climatology.png
│   ├── Figure_6_High_Low_Flow_Days.png
│   ├── Figure_7_Annual_CV.png
│   └── Figure_8_Annual_Streamflow_Trend.png
├── documentation/
│   ├── DATA_SOURCES.md
│   ├── METHODOLOGY.md
│   └── PROCESSING_NOTES.md
└── data/
    └── README.md
```

Large raw datasets and intermediate GIS files were retained in the local working environment rather than included directly in the GitHub repository.

---

## 3. HydroBASINS Processing

HydroBASINS Africa Level 6 data were initially used to identify the general drainage basin associated with the Wudil gauge and to provide a spatial envelope for DEM acquisition.

The relevant HydroBASINS feature had:

* `HYBAS_ID = 1060708880`
* `NEXT_DOWN = 1060700890`
* Upstream area of approximately 24,069.7 km²

The HydroBASINS boundary was **not used as the final watershed boundary**.

### Spatial format conversion

The original HydroBASINS shapefile was converted to GeoPackage format during the DEM acquisition workflow.

This was done because the USGS spatial-input workflow required a compatible spatial dataset format.

> **The HydroBASINS Level 6 shapefile was converted to GeoPackage to satisfy the USGS spatial-input requirement encountered during DEM acquisition.**

The conversion did not change the spatial meaning of the original HydroBASINS feature.

---

## 4. DEM Acquisition and Preparation

### 4.1 DEM source

The elevation data used in the project were obtained from **USGS EarthExplorer** as 30 m SRTM data.

An alternative Copernicus DEM download route was initially considered but did not provide a successful acquisition for this workflow. The project therefore proceeded with the USGS SRTM dataset.

### 4.2 DEM merging

The downloaded SRTM tiles were merged in QGIS to create a continuous DEM covering the study area and surrounding terrain.

The resulting mosaic was:

```text
Hadejia_SRTM_DEM_Mosaic.tif
```

The mosaic was subsequently clipped to the working spatial extent:

```text
Hadejia_SRTM_DEM_Clipped.tif
```

### 4.3 Reprojection

The DEM was initially in geographic coordinates (EPSG:4326).

For watershed analysis involving distances, areas, slopes, and lengths, the DEM was reprojected to:

**WGS 84 / UTM zone 32N — EPSG:32632**

The working DEM became:

```text
Hadejia_SRTM_DEM_UTM32N.tif
```

Using a projected coordinate system ensured that area and distance calculations were performed using metre-based coordinates.

---

## 5. QSWAT Watershed Delineation

QSWAT was used to delineate the working watershed and generate the subbasin and drainage-network layers.

The Wudil gauge location was used to guide the outlet location.

### Main processing settings

* DEM: `Hadejia_SRTM_DEM_UTM32N.tif`
* Stream initiation threshold: approximately **1,088,390 cells**
* Approximate threshold area: **1,008 km²**
* Snap threshold: **300 m**
* Outlet mode: native draw outlet
* Gauge reference: Wudil, GRDC 1837410

The threshold was selected as a project-specific delineation setting to produce a workable drainage network and watershed structure. It is not treated as a universal hydrological threshold.

QSWAT generated:

* watershed boundary
* 10 subbasins
* stream/reach network
* outlet information
* stream-order attributes

---

## 6. Watershed Geometry Correction

The initial QSWAT watershed geometry contained invalid geometry that affected some subsequent GIS operations.

The watershed geometry was therefore processed using the QGIS **Fix Geometries** tool.

The corrected watershed was saved as:

```text
Hadejia_Watershed_Fixed.gpkg
```

and also retained in the geometry-corrected form:

```text
Hadejia_Watershed_Fixed_Geometry.gpkg
```

The corrected layer contains the 10 QSWAT subbasins and was used for subsequent zonal statistics.

This correction was a geometry-processing step and did not represent a change to the intended watershed delineation.

---

## 7. Watershed Boundary for Perimeter Calculation

Subbasin polygons contain internal boundaries shared between adjacent subbasins. Therefore, their individual perimeters were not summed to obtain the watershed perimeter.

Instead, the subbasins were dissolved into a single watershed boundary.

The dissolved boundary was used to calculate the external watershed perimeter:

**2,305.48 km**

This approach avoids counting internal subbasin boundaries as part of the watershed perimeter.

---

## 8. Zonal Statistics Processing

QGIS Zonal Statistics was used to extract elevation statistics from the working DEM for the final watershed/subbasin polygons.

The analysis used:

```text
Hadejia_SRTM_DEM_UTM32N.tif
```

and the geometry-corrected QSWAT watershed/subbasin layer.

The extracted statistics included:

* pixel count
* sum of elevation values
* mean elevation

The watershed-wide mean elevation was subsequently calculated from the total valid raster-cell sum divided by the total valid raster-cell count.

This produced a mean elevation of:

**552.53 m**

The overall elevation range within the final watershed was:

**334.07–1,568.76 m**

---

## 9. Slope Processing and Quality Control

Slope was derived from the projected 30 m SRTM DEM.

The initial slope raster covered the larger working DEM extent rather than only the final watershed.

A later watershed-specific slope raster was created:

```text
Hadejia_Slope_Watershed.tif
```

This raster was clipped to the final QSWAT watershed and used for the final slope map.

### Slope statistics correction

During processing, an earlier zonal-statistics result produced a maximum slope value of approximately **64.48°**.

This value was identified as inconsistent with the validated slope raster used for the final analysis and was therefore **discarded**.

The final watershed-specific slope raster produced a maximum slope of:

**47.30°**

The area-weighted mean slope used in the project was:

**2.16°**

The 2.16° value was obtained from the QGIS zonal-statistics workflow and was not independently recalculated from the raw slope raster in Python.

### Final slope classes

The final map uses three classes:

* **0–5° — Low Slope**
* **5–10° — Moderate Slope**
* **>10° — Steep Slope**

These thresholds were selected for hydrological/cartographic representation within this project and are not presented as universal slope-class standards.

---

## 10. Map 1 DEM Context

The final watershed-specific slope raster was used for the slope analysis and Map 3.

For Map 1, however, the elevation DEM was intentionally retained with some surrounding terrain context rather than being restricted exactly to the watershed boundary.

This was done to provide geographic context around the watershed and gauge location.

The Map 1 DEM therefore functions primarily as an **elevation context layer**, while the watershed-specific raster is used where quantitative slope analysis is required.

This distinction avoids treating the cartographic context extent as the same thing as the analytical watershed extent.

---

## 11. Drainage Network Processing

The drainage network generated through QSWAT was retained as the project's mapped drainage network.

The presentation layer was renamed:

```text
Mapped Drainage Network
```

The network contains 10 mapped reaches with a combined length of approximately:

**791.96 km**

The highest stream order present in the mapped network is:

**3rd order**

The drainage density was calculated from the mapped network length and final watershed area:

$$
D_d=\frac{L}{A}
$$

where:

* \(L\) = mapped drainage length
* \(A\) = watershed area

Result:

**0.0350 km/km²**

Because the drainage network is derived from QSWAT, the result is described throughout the project as **mapped drainage density** rather than as an exhaustive measurement of every natural drainage channel in the watershed.

---

## 12. Subbasin Presentation

QSWAT generated the subbasin layer used for the watershed analysis.

For map presentation, the QSWAT watershed layer was renamed:

```text
Subbasins (QSWAT)
```

The 10 subbasins were symbolised categorically using the `Subbasin` attribute field.

Labels from **1–10** were added to make the individual subbasins visually identifiable.

The presentation layer name does not indicate a separate delineation; it refers to the original QSWAT-derived subbasins.

---

## 13. Land-Cover Processing

ESA WorldCover 2021 was clipped to the final watershed boundary.

The resulting raster:

```text
Hadejia_WorldCover_Watershed.tif
```

was used for the final land-cover map.

The categorical classes were retained from the original ESA WorldCover classification rather than reclassifying them into new land-cover categories.

The final map therefore represents the 2021 land-cover composition of the watershed based on the original WorldCover class definitions.

---

## 14. Streamflow Data Processing

The observed streamflow data were obtained from the GRDC record for:

**Wudil — GRDC Station 1837410**

The original dataset contains a longer period than the period selected for this project.

The analysis was restricted to:

**1 January 1976 – 31 December 1990**

The daily data were read into Python using a whitespace-separated format and the GRDC header was skipped.

The discharge nodata value:

```text
-9999.0
```

was replaced with a missing-value representation before analysis.

The date was reconstructed from the year, month, and day fields.

---

## 15. Streamflow Quality Control

After filtering the selected study period:

* **5,479 daily observations** were available
* Study period: **1976–1990**
* No missing discharge values remained after the filtering and nodata replacement process
* Mean discharge: **35.32 m³/s**
* Median discharge: **14.00 m³/s**
* Maximum recorded discharge: **704.68 m³/s**

The mean being substantially higher than the median reflects the influence of higher-flow observations within the record.

The analysis refers to zero values as **recorded zero-discharge days** rather than assuming that every zero necessarily represents physically verified zero river flow.

---

## 16. Monthly and Annual Aggregation

Monthly mean discharge was calculated by resampling the daily record to calendar-month periods.

Annual mean discharge was calculated by resampling the daily record to calendar-year periods.

This produced:

* **180 monthly mean observations**
* **15 annual mean observations**

The monthly and annual aggregations were derived from the same quality-controlled daily dataset.

---

## 17. Flow-Duration Curve Processing

The flow-duration curve was generated by sorting the daily discharge observations from highest to lowest and assigning an exceedance probability to each observation.

The calculation used:

$$
P_e=\frac{m}{n+1}\times100
$$

where:

* \(P_e\) = exceedance probability
* \(m\) = rank of the discharge value
* \(n\) = total number of observations

The resulting flow-duration curve was plotted using a logarithmic y-axis to improve visibility across the wide range of observed discharge values.

Selected values were:

* **Q10 = 95.9 m³/s**
* **Q50 = 14.0 m³/s**
* **Q90 = 1.9 m³/s**

These values are reported using the flow-duration/exceedance convention used in the analysis.

---

## 18. High- and Low-Flow Thresholds

High- and low-flow days were identified using the 90th and 10th percentiles of the daily discharge distribution.

The resulting thresholds were:

* High-flow threshold: **95.94 m³/s**
* Low-flow threshold: **1.92 m³/s**

This resulted in:

* **552 high-flow days**
* **555 low-flow days**

These are statistical thresholds derived from the study-period distribution and are not fixed hydrological standards.

---

## 19. Recorded Zero-Discharge Days

The daily record contained:

**60 recorded zero-discharge days**

This represents approximately:

**1.10% of the 5,479 daily observations**

The zero-discharge observations were concentrated mainly in:

* 1982
* 1983
* 1984
* 1986
* 1990

The year 1984 contained the largest concentration, accounting for approximately **46.7%** of all recorded zero-discharge observations.

No specific hydrological or climatic cause was assigned to these observations because establishing a cause would require additional supporting data and analysis.

---

## 20. Annual Coefficient of Variation

Annual coefficient of variation was calculated from the annual mean and standard deviation of daily discharge:

$$
CV=\frac{\sigma}{\mu}\times100
$$

where:

* \(\sigma\) = annual standard deviation
* \(\mu\) = annual mean discharge

The resulting annual CV values were used to illustrate the year-to-year variability of daily streamflow within each year.

The CV was not interpreted as a direct measure of a particular hydrological process.

---

## 21. Streamflow Trend Processing

The annual mean discharge series was used for the trend analysis.

A simple linear regression was fitted between year and annual mean discharge.

The final regression produced:

* Slope: **−0.2098 m³/s/year**
* Intercept: **451.2557**
* R²: **0.0029**
* p-value: **0.8485**

The trend line was therefore retained as a descriptive statistical analysis.

The very small R² indicates that the fitted linear relationship explains very little of the observed variation in annual mean discharge. The p-value does not provide statistical evidence of a detectable linear trend over the selected 1976–1990 period.

No causal explanation was assigned to the fitted trend because rainfall, evaporation, abstraction, reservoir operations, land-use change, and other potential explanatory variables were not jointly modelled.

---

## 22. Notebook Correction

During preparation of the annual trend figure, the plotting cell initially referenced `trend_values` before the variable had been created.

The issue was corrected by calculating the regression first and then explicitly generating the fitted trend values:

```python
trend = linregress(years, annual_values)
trend_values = trend.intercept + trend.slope * years
```

The trend figure was then regenerated and checked before export.

This correction affected the plotting workflow only and did not change the underlying annual streamflow data or regression results.

---

## 23. Figure Export

The eight streamflow figures generated in the Jupyter notebook were exported as PNG files and organised in the repository's `figures/` directory.

The final figures include:

1. Daily Streamflow
2. Monthly Mean Streamflow
3. Annual Mean Streamflow
4. Flow-Duration Curve
5. Monthly Climatology
6. High- and Low-Flow Days
7. Annual Coefficient of Variation
8. Annual Streamflow Trend

The figures were reviewed after export to ensure that titles, axes, legends, annotations, and labels were readable.

---

## 24. GIS and Python Roles

The project deliberately separates the roles of the GIS and Python workflows.

### QGIS / QSWAT

Used for:

* DEM preparation
* CRS transformation
* watershed delineation
* subbasin generation
* drainage-network extraction
* elevation analysis
* slope generation
* zonal statistics
* watershed morphometric calculations
* land-cover processing
* map production

### Python / Jupyter

Used for:

* GRDC streamflow data preparation
* quality control
* daily streamflow analysis
* monthly aggregation
* annual aggregation
* flow-duration analysis
* high/low-flow threshold analysis
* monthly climatology
* zero-discharge analysis
* annual coefficient of variation
* linear trend analysis
* figure generation

This separation is intentional: the GIS workflow describes the **physical watershed context**, while the Python workflow analyses the **observed streamflow record**.

---

## 25. SWAT/QSWAT Scope

QSWAT was used for watershed delineation and extraction of subbasins and drainage features.

A complete SWAT hydrological simulation involving soil, land-use/HRU definition, weather forcing, calibration, and validation was not included in the final project.

The project therefore should not be described as a calibrated SWAT rainfall-runoff modelling study.

The final assessment combines:

1. GIS-derived watershed characteristics, and
2. statistical analysis of observed streamflow.

---

## 26. Reproducibility Notes

To reproduce the analysis:

1. Obtain the required source datasets.
2. Prepare and project the SRTM DEM to EPSG:32632.
3. Use the Wudil gauge location as the outlet reference.
4. Recreate the QSWAT watershed delineation using the documented project settings.
5. Fix invalid watershed geometry before zonal statistics.
6. Generate the watershed-specific slope raster.
7. Prepare the ESA WorldCover raster for the final watershed.
8. Obtain the GRDC Wudil daily streamflow record.
9. Filter the streamflow data to 1976–1990.
10. Run the Python notebook from the beginning to reproduce the statistical analyses and figures.
11. Export the final maps from QGIS using the documented layer structure and symbology.

Because some source datasets and intermediate GIS files are not distributed with the repository, exact reproduction may require downloading the original datasets from their respective providers.

---

## 27. Interpretation Boundaries

The following points should be kept in mind when interpreting the project:

* The final watershed boundary is **QSWAT-derived**, not the HydroBASINS Level 6 boundary.
* HydroBASINS was used as a preliminary reference and DEM acquisition envelope.
* The drainage network represents the **mapped QSWAT-derived network**, not necessarily every drainage channel in the watershed.
* The drainage density is therefore described as **mapped drainage density**.
* The 2.16° mean slope is based on the final watershed slope analysis.
* The 47.30° maximum slope belongs to the final watershed-specific slope raster.
* The 552.53 m mean elevation is a watershed-level zonal-statistics result.
* The streamflow analysis describes the Wudil gauge record for 1976–1990 and should not automatically be treated as representative of every location within the watershed.
* Recorded zero-discharge values are reported as recorded observations and are not independently interpreted as confirmed physical zero flow.
* The linear trend analysis does not establish the causes of streamflow variation.
* The study does not provide a calibrated rainfall-runoff model.

---

## 28. Final Processing Summary

The processing workflow progressed from broad spatial reference data to a final QSWAT-derived watershed, followed by GIS-based terrain and drainage analysis and Python-based observed streamflow analysis.

The main processing chain was:

```text
HydroBASINS reference
        ↓
SRTM DEM acquisition
        ↓
DEM merge and preparation
        ↓
Reprojection to EPSG:32632
        ↓
QSWAT watershed delineation
        ↓
Geometry correction
        ↓
Subbasins + drainage network
        ↓
Terrain and morphometric analysis
        ↓
Watershed-specific slope processing
        ↓
ESA WorldCover clipping
        ↓
Final maps

GRDC Wudil daily streamflow
        ↓
Quality control
        ↓
1976–1990 study-period filtering
        ↓
Daily / monthly / annual analysis
        ↓
FDC + flow thresholds
        ↓
Climatology + variability
        ↓
Linear trend analysis
        ↓
Final figures
```

The resulting workflow provides a reproducible GIS and Python framework for examining the physical characteristics of the Hadejia River watershed alongside observed streamflow behaviour at the Wudil gauge.
