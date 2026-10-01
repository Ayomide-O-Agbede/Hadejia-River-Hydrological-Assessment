# Processing Notes

## 1. Purpose

This document records the main processing decisions and quality-control steps used to prepare the GIS layers, watershed outputs, and streamflow analysis for the **Hadejia River Watershed Hydrological Assessment**.

It complements `METHODOLOGY.md` by documenting important implementation decisions without providing a step-by-step software tutorial.

---

## 2. Spatial Data Preparation

### HydroBASINS

HydroBASINS Africa Level 6 data were used as an initial spatial reference for identifying the drainage basin associated with the Wudil gauge and for defining a suitable area for DEM acquisition.

The relevant Level 6 feature had:

* `HYBAS_ID = 1060708880`
* `NEXT_DOWN = 1060700890`
* Upstream area ≈ **24,069.7 km²**

The HydroBASINS boundary was **not used as the final watershed boundary**.

The original shapefile was converted to GeoPackage format to satisfy the spatial-input requirement encountered during the USGS DEM acquisition workflow.

### SRTM DEM

A 30 m SRTM DEM was obtained from **USGS EarthExplorer**.

The downloaded tiles were merged into a continuous DEM and subsequently clipped to the working study area.

The DEM was reprojected from geographic coordinates (EPSG:4326) to:

**WGS 84 / UTM zone 32N — EPSG:32632**

The projected DEM was used for watershed delineation and distance- and area-based calculations.

---

## 3. Watershed Delineation

The final watershed was delineated using **QSWAT** from the prepared 30 m SRTM DEM.

The Wudil gauge location (GRDC 1837410) was used as the outlet reference.

The main project settings included:

* Stream initiation threshold: approximately **1,088,390 cells**
* Approximate threshold area: **1,008 km²**
* Snap threshold: **300 m**
* Native outlet placement near the Wudil gauge

The delineation produced:

* **1 final watershed**
* **10 subbasins**
* A QSWAT-derived mapped drainage network

The threshold was treated as a project-specific delineation setting rather than a universal hydrological standard.

---

## 4. Geometry Quality Control

The initial QSWAT watershed geometry contained invalid geometry affecting subsequent spatial analysis.

The watershed geometry was therefore corrected using the QGIS **Fix Geometries** tool before further analysis.

The corrected geometry was subsequently used for:

* area calculations
* elevation zonal statistics
* slope analysis
* watershed boundary preparation
* land-cover clipping

The geometry correction did not represent a change to the intended watershed delineation.

---

## 5. Watershed and Terrain Analysis

### Area

The final watershed area was calculated from the corrected projected geometry.

Total area:

**22,619.66 km²**

Subbasin area contributions were calculated relative to this total watershed area.

### Perimeter

The subbasin polygons were dissolved before calculating the watershed perimeter so that internal subbasin boundaries were not counted.

Final watershed perimeter:

**2,305.48 km**

### Elevation

Elevation statistics were extracted using QGIS zonal statistics from the projected SRTM DEM.

Final watershed statistics included:

* Minimum elevation: **334.07 m**
* Maximum elevation: **1,568.76 m**
* Mean elevation: **552.53 m**
* Relief: **1,234.69 m**

The mean elevation represents the final watershed area analysed using zonal statistics, rather than the entire original DEM acquisition extent.

---

## 6. Slope Processing

Slope was derived from the projected 30 m SRTM DEM.

A watershed-specific slope raster was subsequently produced by clipping the slope surface to the final watershed boundary.

The final slope analysis produced:

* Area-weighted mean slope: **2.16°**
* Maximum slope: **47.30°**

An earlier inconsistent maximum-slope result was identified during quality control and was not used in the final analysis.

The final map uses three project-defined classes:

* **0–5° — Low Slope**
* **5–10° — Moderate Slope**
* **>10° — Steep Slope**

These classes were selected for hydrological/cartographic representation and are not presented as universal slope-class standards.

The area-weighted mean slope was obtained through the QGIS zonal-statistics workflow.

---

## 7. Drainage Network

The drainage network used in the assessment was derived from the QSWAT watershed delineation.

The final mapped network contains:

* **791.96 km** of mapped reaches
* Maximum mapped stream order: **3rd order**

Mapped drainage density was calculated as:

$$
D_d=\frac{L}{A}
$$

where \(L\) is the mapped drainage length and \(A\) is the watershed area.

Result:

**0.0350 km/km²**

Because the network is QSWAT-derived, the result is described as **mapped drainage density** rather than as a complete inventory of all natural drainage channels.

---

## 8. Land-Cover Processing

ESA WorldCover 2021 was clipped to the final watershed boundary.

The resulting watershed-specific raster was used for the final land-cover map.

The original ESA WorldCover categorical classes were retained rather than replaced with project-specific land-cover categories.

---

## 9. Streamflow Data Processing

Daily observed streamflow data were obtained from the **GRDC Wudil gauge (Station 1837410)**.

The original record covers a longer period than the selected analysis period.

For this project, the data were restricted to:

**1976–1990**

The GRDC nodata value was removed before analysis, and the date fields were combined to create a continuous date variable.

After quality control:

* **5,479 daily observations**
* No missing discharge values within the selected study period
* Mean discharge: **35.32 m³/s**
* Median discharge: **14.00 m³/s**
* Maximum recorded discharge: **704.68 m³/s**

---

## 10. Streamflow Analysis

The quality-controlled daily record was used to derive:

* monthly mean discharge
* annual mean discharge
* flow-duration curve
* monthly climatology
* high- and low-flow thresholds
* recorded zero-discharge days
* annual coefficient of variation
* annual streamflow trend

The resulting analyses were generated in the Jupyter notebook:

```text
notebook/Hadejia_Streamflow_Hydrological_Analysis.ipynb
```

### Flow-duration analysis

The flow-duration curve was based on ranked daily discharge values and exceedance probability.

Selected values were:

* **Q10 = 95.9 m³/s**
* **Q50 = 14.0 m³/s**
* **Q90 = 1.9 m³/s**

### Flow thresholds

The 90th and 10th percentiles of the daily discharge distribution were used to identify statistical high- and low-flow thresholds.

* High-flow threshold: **95.94 m³/s**
* Low-flow threshold: **1.92 m³/s**

### Recorded zero-discharge days

The record contained:

**60 recorded zero-discharge days**

These represented approximately **1.10%** of the observations.

They were reported as recorded zero-discharge observations rather than being assigned a specific physical cause.

---

## 11. Streamflow Variability

Annual coefficient of variation was calculated as:

$$
CV=\frac{\sigma}{\mu}\times100
$$

where:

* \(\sigma\) = annual standard deviation
* \(\mu\) = annual mean discharge

The resulting values were used to describe year-to-year variability within the observed record.

---

## 12. Trend Analysis

A simple linear regression was applied to annual mean discharge for 1976–1990.

The resulting statistics were:

* Slope: **−0.2098 m³/s/year**
* Intercept: **451.2557**
* R²: **0.0029**
* p-value: **0.8485**

The regression was treated as a descriptive statistical analysis. No causal explanation was assigned to the observed variation because additional explanatory variables such as rainfall, evaporation, abstraction, and reservoir operations were not jointly modelled.

---

## 13. GIS and Python Roles

The project used QGIS/QSWAT and Python for complementary purposes.

### QGIS / QSWAT

Used for:

* DEM preparation
* CRS transformation
* watershed delineation
* subbasin generation
* drainage-network extraction
* elevation analysis
* slope analysis
* zonal statistics
* land-cover processing
* map production

### Python / Jupyter

Used for:

* GRDC data preparation
* quality control
* daily streamflow analysis
* monthly and annual aggregation
* flow-duration analysis
* flow-threshold analysis
* monthly climatology
* variability analysis
* linear trend analysis
* figure generation

The GIS workflow provides the **physical watershed context**, while the Python workflow analyses the **observed streamflow behaviour**.

---

## 14. Presentation and Output Preparation

Four final maps were produced:

1. **Watershed Overview and Gauge Location**
2. **Subbasins and Drainage Network**
3. **Slope Map**
4. **Land-Cover Map**

Eight streamflow figures were produced from the Python notebook.

The final outputs were organised in:

```text
maps/
figures/
```

The README provides the main presentation layer, while the supporting documentation provides methodological and data-related context.

---

## 15. Scope of QSWAT Use

QSWAT was used for watershed delineation and extraction of subbasins and drainage features.

A complete SWAT rainfall-runoff simulation was **not** included in the final assessment.

The project therefore should not be described as a calibrated SWAT modelling study.

Instead, it combines:

1. GIS-derived watershed characteristics; and
2. Statistical analysis of observed streamflow at the Wudil gauge.

---

## 16. Interpretation Boundaries

The following distinctions are important when interpreting the results:

* The final watershed boundary is **QSWAT-derived**, not the HydroBASINS Level 6 boundary.
* HydroBASINS was used as an initial spatial reference.
* The drainage network represents the **mapped QSWAT-derived network**.
* The drainage density is therefore described as **mapped drainage density**.
* The 47.30° maximum slope refers to the final watershed-specific slope raster.
* The 552.53 m mean elevation is a watershed-level zonal-statistics result.
* The streamflow analysis represents the Wudil gauge record for **1976–1990** and does not automatically represent every location within the watershed.
* Recorded zero-discharge observations were not assigned a specific cause.
* The linear trend analysis does not establish the causes of streamflow variation.
* The project does not include calibrated rainfall-runoff modelling.

---

## 17. Processing Summary

The overall processing sequence was:

```text
HydroBASINS reference
        ↓
SRTM DEM acquisition and preparation
        ↓
DEM reprojection
        ↓
QSWAT watershed delineation
        ↓
Geometry quality control
        ↓
Subbasins and drainage network
        ↓
Terrain and morphometric analysis
        ↓
Slope and land-cover processing
        ↓
Final GIS maps

GRDC Wudil streamflow
        ↓
Quality control
        ↓
1976–1990 filtering
        ↓
Daily / monthly / annual analysis
        ↓
FDC and flow thresholds
        ↓
Climatology and variability
        ↓
Trend analysis
        ↓
Final figures
```

This processing structure provides sufficient information to understand how the project outputs were produced while keeping the repository focused on the scientific and analytical decisions rather than detailed software troubleshooting.
