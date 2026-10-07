# Processing Notes

This document records the main processing decisions, corrections and quality-control steps used in the **Hadejia River Watershed Hydrological Assessment**.

It complements `Methodology.md` by documenting how the final datasets and outputs were prepared.

## 1. HydroBASINS Processing

HydroBASINS Africa Level 6 was used as an initial spatial reference for the Hadejia drainage basin and for defining a suitable area for DEM acquisition.

The relevant feature had:

* `HYBAS_ID = 1060708880`
* `NEXT_DOWN = 1060700890`
* Upstream area ≈ **24,069.7 km²**

HydroBASINS was **not used as the final watershed boundary**.

The original shapefile was converted to GeoPackage format during the DEM acquisition workflow to meet the spatial-input requirement encountered during the process.

## 2. SRTM DEM Processing

A 30 m SRTM DEM was obtained from USGS EarthExplorer.

The downloaded tiles were:

1. Merged into a continuous DEM.
2. Clipped to the working area.
3. Reprojected from EPSG:4326 to **EPSG:32632**.
4. Used as the working elevation surface for QSWAT and terrain analysis.

The main processed DEM was:

```text
Hadejia_SRTM_DEM_UTM32N.tif
```

## 3. QSWAT Watershed Delineation

The final watershed was delineated using QSWAT with the Wudil gauge as the outlet reference.

Main settings:

* Stream threshold: approximately **1,088,390 cells**
* Equivalent threshold area: approximately **1,008 km²**
* Snap threshold: **300 m**

The process produced:

* 1 watershed
* 10 subbasins
* QSWAT-derived drainage network
* Snapped outlet

The threshold values are project-specific settings and are not treated as universal hydrological standards.

## 4. Geometry Quality Control

The initial QSWAT watershed geometry contained invalid geometry.

The geometry was corrected using the QGIS **Fix Geometries** tool.

The corrected geometry was then used for:

* Area calculations
* Perimeter calculation
* Elevation zonal statistics
* Slope analysis
* Land-cover clipping
* Final watershed mapping

The geometry correction was a quality-control step and did not represent a change to the intended QSWAT delineation.

## 5. Watershed and Terrain Processing

The corrected watershed produced a final area of:

**22,619.66 km²**

The subbasins were dissolved before calculating the watershed perimeter, giving:

**2,305.48 km**

Elevation statistics from the final watershed were:

* Minimum: **334.07 m**
* Maximum: **1,568.76 m**
* Mean: **552.53 m**
* Relief: **1,234.69 m**

The elevation statistics refer to the final watershed area, not the original DEM acquisition extent.

## 6. Slope Quality Control

Slope was derived from the projected 30 m SRTM DEM and clipped to the final watershed.

The final watershed-specific slope raster produced:

* Area-weighted mean slope: **2.16°**
* Maximum slope: **47.30°**

An earlier inconsistent maximum-slope value was identified during quality control and was not used in the final results.

The final map uses:

* **0–5° — Low Slope**
* **5–10° — Moderate Slope**
* **>10° — Steep Slope**

These are project-defined classes for analysis and map presentation.

## 7. Drainage Network Processing

The drainage network was taken from the QSWAT watershed delineation.

The final mapped network contained:

* **10 reaches**
* **791.96 km** total mapped length
* Maximum mapped stream order: **3rd order**

The stream-order distribution was:

* 1st order: 6 reaches
* 2nd order: 2 reaches
* 3rd order: 2 reaches

Drainage density was calculated from the mapped network:

$$
D_d=\frac{791.96}{22,619.66}
$$

giving:

**0.0350 km/km²**

The result is reported as **mapped drainage density** because it represents the QSWAT-derived network rather than a complete inventory of all drainage channels.

## 8. Land-Cover Processing

ESA WorldCover 2021 was clipped to the final watershed boundary.

The original ESA WorldCover classes were retained rather than replaced with project-specific categories.

The resulting watershed-specific raster was used for the final land-cover map.

## 9. Streamflow Data Quality Control

Daily discharge data were obtained from the **GRDC Wudil gauge, Station 1837410**.

The selected analysis period was:

**1976–1990**

The GRDC missing-value code was removed before analysis and the year, month and day fields were combined into a date field.

After filtering and quality control:

* **5,479 daily observations**
* No missing discharge values within the selected period
* Mean discharge: **35.32 m³/s**
* Median discharge: **14.00 m³/s**
* Maximum recorded discharge: **704.68 m³/s**

The analysis retained the recorded discharge values rather than applying additional gap filling or smoothing.

## 10. Streamflow Analysis Processing

The quality-controlled record was analysed in:

```text
notebook/Hadejia_Streamflow_Hydrological_Analysis.ipynb
```

The notebook produced:

* Daily hydrograph
* Monthly mean discharge
* Annual mean discharge
* Flow Duration Curve
* High- and low-flow thresholds
* Monthly climatology
* Recorded zero-discharge analysis
* Annual coefficient of variation
* Annual trend analysis

### Flow Duration Curve

Selected FDC values were:

* Q10 = **95.9 m³/s**
* Q50 = **14.0 m³/s**
* Q90 = **1.9 m³/s**

### Flow Thresholds

The 90th and 10th percentiles of daily discharge were used as descriptive high- and low-flow thresholds:

* High-flow threshold: **95.94 m³/s**
* Low-flow threshold: **1.92 m³/s**
* High-flow days: **552**
* Low-flow days: **555**

### Zero-Discharge Observations

The record contained:

**60 recorded zero-discharge days**

This represents approximately **1.10%** of the full record.

The observations were retained as recorded values and were not assigned a specific physical cause.

## 11. Variability and Trend Processing

Annual coefficient of variation was calculated from the mean and standard deviation of daily discharge for each year.

The annual CV ranged from:

**98.01% to 198.46%**

A simple linear regression was then applied to annual mean discharge.

Results:

* Slope: **−0.2098 m³/s/year**
* Intercept: **451.2557**
* R²: **0.0029**
* p-value: **0.8485**

The regression was treated as a descriptive analysis. No causal explanation was assigned to the observed variation because rainfall, evaporation, abstraction, reservoir operations and other possible explanatory variables were not jointly analysed.

## 12. GIS and Python Roles

The project deliberately separated the spatial and streamflow workflows.

### QGIS / QSWAT

Used for:

* DEM preparation
* CRS transformation
* Watershed delineation
* Subbasin generation
* Drainage extraction
* Elevation analysis
* Slope analysis
* Zonal statistics
* Land-cover processing
* Map production

### Python / Jupyter

Used for:

* GRDC data preparation
* Quality control
* Daily streamflow analysis
* Monthly and annual aggregation
* Flow-duration analysis
* Flow-threshold analysis
* Monthly climatology
* Variability analysis
* Linear trend analysis
* Figure generation

The GIS workflow provides the **physical watershed context**, while the Python workflow analyses the **observed streamflow behaviour**.

## 13. Output Preparation

Four final maps were produced:

1. Watershed Overview and Gauge Location
2. Subbasins and Drainage Network
3. Slope
4. Land Cover

Eight streamflow figures were produced from the Python notebook.

The final outputs were organised into:

```text
maps/
figures/
```

The streamflow notebook is stored in:

```text
notebook/Hadejia_Streamflow_Hydrological_Analysis.ipynb
```

## 14. Important Interpretation Boundaries

The following points should be kept in mind when using the results:

* The final watershed boundary is **QSWAT-derived**, not the HydroBASINS Level 6 boundary.
* HydroBASINS was used only as an initial spatial reference.
* The drainage network is the **mapped QSWAT-derived network**.
* Drainage density is therefore reported as **mapped drainage density**.
* The **47.30°** maximum slope refers to the final watershed-specific slope raster.
* The **552.53 m** mean elevation is a watershed-level zonal-statistics result.
* The streamflow analysis represents the Wudil gauge record for **1976–1990** and does not automatically represent every location in the watershed.
* Recorded zero-discharge values were not independently field-validated.
* The trend analysis does not establish the causes of streamflow variation.
* No calibrated rainfall-runoff model was produced.

## 15. QSWAT Scope

QSWAT was used for:

* Watershed delineation
* Subbasin generation
* Drainage-network extraction

A complete SWAT rainfall-runoff simulation was **not** included.

The project should therefore be described as a combination of:

1. **GIS-derived watershed and terrain analysis**, and
2. **Statistical analysis of observed streamflow**.

It should not be described as a calibrated SWAT modelling study.
