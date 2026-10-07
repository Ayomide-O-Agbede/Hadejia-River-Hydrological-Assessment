# Methodology

This document describes the analytical methods used for the **Hadejia River Watershed Hydrological Assessment**.

The workflow combines spatial watershed analysis in **QGIS/QSWAT** with observed streamflow analysis in **Python/Jupyter Notebook**.

## 1. Analytical Framework

The analysis was divided into two main components.

### Spatial watershed analysis

QGIS and QSWAT were used for:

* DEM preparation
* Watershed and subbasin delineation
* Elevation and slope analysis
* Drainage-network extraction
* Morphometric calculations
* Land-cover processing
* Map production

### Observed streamflow analysis

Python and Jupyter Notebook were used for:

* Data preparation and quality control
* Daily streamflow analysis
* Monthly and annual aggregation
* Flow Duration Curve analysis
* High- and low-flow analysis
* Zero-discharge analysis
* Monthly climatology
* Annual coefficient of variation
* Linear trend analysis

The two components were combined during the final interpretation.

## 2. Spatial Reference System

The main spatial analysis used:

**WGS 84 / UTM Zone 32N — EPSG:32632**

The projected CRS was used for area, perimeter, drainage-length and other distance-based calculations.

## 3. DEM Preparation

A **30 m SRTM DEM** was obtained from USGS EarthExplorer.

HydroBASINS Africa Level 6 was first used as a preliminary spatial reference and to define a suitable acquisition extent.

The SRTM tiles were:

1. Mosaicked.
2. Clipped to the working extent.
3. Reprojected from EPSG:4326 to EPSG:32632.
4. Used as the working elevation surface for QSWAT and terrain analysis.

The main processed DEM was:

```text
Hadejia_SRTM_DEM_UTM32N.tif
```

## 4. Watershed Delineation

The final watershed was delineated using QSWAT from the processed 30 m SRTM DEM.

The main inputs were:

* SRTM DEM
* Wudil gauge location
* Outlet/snap location
* Stream-definition threshold

The main delineation settings were:

* Stream threshold: approximately **1,088,390 DEM cells**
* Equivalent threshold area: approximately **1,008 km²**
* Snap threshold: **300 m**

The delineation produced:

* 1 final watershed
* 10 subbasins
* A QSWAT-derived drainage network
* A snapped outlet near the Wudil gauge

The initial watershed geometry contained invalid geometry and was corrected using the QGIS **Fix Geometries** tool before further spatial calculations.

## 5. Watershed Morphometry

### Area

The corrected projected watershed/subbasin geometries were used to calculate area.

Total watershed area:

**22,619.66 km²**

Subbasin contribution was calculated as:

$$
A_i(\%)=\frac{A_i}{A_{total}}\times100
$$

### Perimeter

The subbasins were dissolved into one watershed boundary before calculating perimeter so that internal subbasin boundaries were not included.

Final perimeter:

**2,305.48 km**

### Elevation and Relief

Elevation statistics were obtained using QGIS Zonal Statistics and the processed SRTM DEM.

Results:

* Minimum: **334.07 m**
* Maximum: **1,568.76 m**
* Mean: **552.53 m**

Relief was calculated as:

$$
R=E_{max}-E_{min}
$$

giving:

**1,234.69 m**

### Slope

Slope was derived from the projected 30 m SRTM DEM and clipped to the final watershed.

The final watershed-specific slope raster was:

```text
Hadejia_Slope_Watershed.tif
```

Results:

* Maximum slope: **47.30°**
* Area-weighted mean slope: **2.16°**

The final map used three project-defined classes:

| Slope | Class          |
| ----- | -------------- |
| 0–5°  | Low Slope      |
| 5–10° | Moderate Slope |
| >10°  | Steep Slope    |

These classes were selected for project representation and are not presented as universal hydrological standards.

### Drainage Network

The drainage network was derived by QSWAT.

The final mapped network contained:

* **10 mapped reaches**
* **791.96 km** total mapped length
* Maximum mapped stream order: **3rd order**

The mapped drainage density was calculated as:

$$
D_d=\frac{L}{A}
$$

giving:

**0.0350 km/km²**

Because the drainage network is QSWAT-derived, this is reported as **mapped drainage density**.

Mapped reach frequency was calculated as:

$$
F_s=\frac{N}{A}
$$

giving:

**0.000442 reaches/km²**

### Compactness Coefficient

The compactness coefficient was calculated using:

$$
K_c=\frac{P}{2\sqrt{\pi A}}
$$

Using the final watershed area and perimeter produced:

**Kc = 4.32**

The value is reported as a descriptive morphometric measure without assigning a qualitative ranking.

## 6. Land-Cover Analysis

ESA WorldCover 2021 was used to represent land cover within the final watershed.

The raster was prepared and clipped to the final watershed boundary.

The original classes were retained:

* Tree cover
* Shrubland
* Grassland
* Cropland
* Built-up
* Bare/sparse vegetation
* Permanent water bodies
* Herbaceous wetland

The analysis was descriptive and was not used to model land-cover effects on runoff.

## 7. Streamflow Data Preparation

Daily observed discharge data from the **GRDC Wudil gauge, Station 1837410**, were processed in Python.

The analysis period was restricted to:

**1 January 1976 – 31 December 1990**

The GRDC missing-value code (`-9999`) was treated as missing before analysis.

The date fields were combined into a single date variable and the selected study period was extracted.

After quality control:

* **5,479 observations**
* No missing discharge values within the selected period

## 8. Daily and Monthly Streamflow Analysis

The daily record was plotted to examine:

* Short-term variability
* High-flow events
* Low-flow periods
* Seasonal behaviour
* Year-to-year variation

Daily discharge was aggregated to monthly and annual means.

The study period produced **180 monthly values**.

## 9. Flow Duration Curve

The Flow Duration Curve was produced by ranking the daily discharge values from highest to lowest and calculating exceedance probability as:

$$
P_e=\frac{m}{n+1}\times100
$$

where \(m\) is the rank and \(n\) is the number of observations.

Selected values were:

* **Q10 = 95.9 m³/s**
* **Q50 = 14.0 m³/s**
* **Q90 = 1.9 m³/s**

Q10 represents discharge exceeded approximately 10% of the time, while Q90 represents discharge exceeded approximately 90% of the time.

## 10. High- and Low-Flow Analysis

The 90th and 10th percentiles of the daily discharge distribution were used as descriptive thresholds.

Results:

* High-flow threshold: **95.94 m³/s**
* Low-flow threshold: **1.92 m³/s**
* High-flow days: **552**
* Low-flow days: **555**

These are statistical thresholds based on the observed distribution.

## 11. Recorded Zero-Discharge Days

The cleaned record contained:

**60 recorded zero-discharge days**

This represents approximately:

**1.10%** of the 5,479 observations.

The values were retained as recorded observations and were not assigned a specific physical cause.

## 12. Monthly Streamflow Climatology

Monthly climatology was calculated by grouping daily discharge by calendar month across the 1976–1990 record.

| Month     | Mean Discharge (m³/s) |
| --------- | --------------------: |
| January   |                 10.47 |
| February  |                 11.13 |
| March     |                 11.27 |
| April     |                 11.18 |
| May       |                 17.80 |
| June      |                 34.56 |
| July      |                 68.38 |
| August    |                115.80 |
| September |                 83.21 |
| October   |                 26.15 |
| November  |                 15.87 |
| December  |                 15.96 |

The highest average monthly discharge occurred in **August at 115.80 m³/s**.

## 13. Annual Coefficient of Variation

Annual coefficient of variation was calculated from the standard deviation and mean of daily discharge for each year:

$$
CV(\%)=\frac{\sigma}{\bar{Q}}\times100
$$

The annual CV values ranged from:

**98.01% to 198.46%**

This was used as a descriptive measure of within-year discharge variability.

## 14. Streamflow Trend Analysis

A simple linear regression was applied to annual mean discharge for 1976–1990.

The fitted relationship was:

$$
Q_t=a+bt
$$

The results were:

| Metric    |                Result |
| --------- | --------------------: |
| Slope     | **−0.2098 m³/s/year** |
| Intercept |          **451.2557** |
| R²        |            **0.0029** |
| p-value   |            **0.8485** |

The very low R² indicates that the fitted linear relationship explains very little of the variation in annual mean discharge.

The p-value does not provide statistical evidence of a detectable linear trend during the study period.

The analysis is descriptive and does not establish the causes of the observed variation.

## 15. Integrated Interpretation

The final interpretation considers both:

### Physical watershed characteristics

* Area and perimeter
* Elevation and relief
* Slope
* Drainage length and density
* Stream order
* Land cover

### Observed hydrological behaviour

* Mean and median discharge
* Flow-duration characteristics
* Seasonal variation
* High- and low-flow conditions
* Recorded zero-discharge days
* Annual variability
* Linear trend

The analysis describes the relationship between the physical setting and observed streamflow behaviour but does not establish causal relationships between rainfall, land cover, groundwater, terrain, abstraction or other hydrological controls.

## 16. Software and Analytical Environment

The project used:

* **QGIS** — GIS processing, terrain analysis, spatial calculations and mapping
* **QSWAT** — watershed/subbasin delineation and drainage-network extraction
* **Python** — streamflow processing and statistical analysis
* **Jupyter Notebook** — reproducible analysis and figure generation
* **Pandas** — time-series processing
* **NumPy** — numerical calculations
* **SciPy** — linear regression
* **Matplotlib** — visualization

The spatial and streamflow workflows were kept separate during processing and combined during interpretation.
