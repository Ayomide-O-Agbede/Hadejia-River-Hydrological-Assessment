# Methodology

This document describes the analytical methods used in the **Hadejia River Watershed Hydrological Assessment**.

The project combines **GIS and watershed analysis in QGIS/QSWAT** with **observed streamflow analysis in Python/Jupyter Notebook**.

The workflow was designed to characterize the physical watershed, its terrain and mapped drainage network, and the temporal behaviour of observed streamflow at the Wudil gauge.

---

# 1. Overall Analytical Framework

The project was divided into two main analytical components:

### Spatial watershed analysis

Conducted primarily in **QGIS and QSWAT**:

* DEM preparation
* Watershed delineation
* Subbasin delineation
* Elevation analysis
* Slope analysis
* Drainage-network extraction
* Morphometric analysis
* Land-cover analysis
* Cartographic production

### Observed streamflow analysis

Conducted in **Python/Jupyter Notebook**:

* Data loading and quality control
* Daily streamflow analysis
* Monthly and annual aggregation
* Flow Duration Curve
* High- and low-flow analysis
* Zero-discharge analysis
* Monthly climatology
* Annual coefficient of variation
* Linear trend analysis

The **area-weighted mean slope** was calculated from QGIS-derived zonal statistics and incorporated into the integrated hydrological interpretation.

---

# 2. Spatial Reference System

The main spatial analysis was performed using:

**WGS 84 / UTM Zone 32N**

**EPSG:32632**

The projected coordinate system was used because the project required distance and area calculations in metres and kilometres.

---

# 3. DEM Preparation

## 3.1 SRTM DEM Acquisition

A 30 m SRTM DEM was obtained from USGS EarthExplorer.

HydroBASINS Africa Level 6 was first used as a preliminary geographic reference to identify the area required for DEM acquisition.

The downloaded SRTM tiles were then combined into a continuous DEM mosaic.

---

## 3.2 DEM Processing

The DEM workflow consisted of:

1. Loading the SRTM tiles into QGIS.
2. Mosaicking the tiles.
3. Clipping the mosaic to the working geographic extent.
4. Reprojecting the DEM to EPSG:32632.
5. Using the reprojected DEM as the working elevation surface for QSWAT and terrain analysis.

The main processed DEM was:

```text
Hadejia_SRTM_DEM_UTM32N.tif
```

---

# 4. Watershed Delineation

## 4.1 QSWAT Setup

The final watershed was delineated using **QSWAT**.

The principal inputs were:

* Processed 30 m SRTM DEM
* Wudil gauge location
* Outlet/snap location
* Stream-definition threshold

The Wudil gauge was used to guide the watershed outlet.

A stream threshold of approximately:

**1,088,390 DEM cells**

was used during watershed delineation.

At the working DEM resolution, this represented approximately:

**1,008 km²**

of contributing area for stream initiation.

A snap threshold of:

**300 m**

was used to associate the outlet with the derived drainage network.

---

## 4.2 Final Watershed

QSWAT generated:

* Final watershed boundary
* **10 subbasins**
* Mapped drainage network
* Snapped outlet

The original watershed geometry contained invalid geometry and was corrected using the QGIS **Fix Geometries** tool.

The corrected geometry was then used for the subsequent spatial calculations.

---

# 5. Watershed Area

The area of each fixed watershed/subbasin polygon was obtained in square metres using the projected EPSG:32632 geometry.

The total watershed area was calculated by summing the polygon areas:

$$
A_{total}=\sum_{i=1}^{n} A_i
$$

The result in square metres was converted to square kilometres using:

$$
A_{km^2}=\frac{A_{m^2}}{1,000,000}
$$

The calculated total area was:

$$
A_{total}=22,619,662,243\;m^2
$$

Therefore:

$$
A_{total}=22,619.66\;km^2
$$

---

# 6. Subbasin Area Contribution

The percentage contribution of each subbasin to the total watershed area was calculated using:

$$
A_i(\%)=
\frac{A_i}{A_{total}}\times100
$$

where:

* \(A_i\) = area of subbasin \(i\)
* \(A_{total}\) = total watershed area

The resulting subbasin contributions were:

| Subbasin  |    Area (km²) | Contribution (%) |
| --------- | ------------: | ---------------: |
| 1         |        411.65 |             1.82 |
| 2         |      4,293.53 |            18.98 |
| 3         |        100.14 |             0.44 |
| 4         |      1,274.81 |             5.64 |
| 5         |      4,995.22 |            22.08 |
| 6         |      1,405.77 |             6.21 |
| 7         |      5,328.62 |            23.56 |
| 8         |      2,115.67 |             9.35 |
| 9         |      1,535.41 |             6.79 |
| 10        |      1,158.85 |             5.12 |
| **Total** | **22,619.66** |       **100.00** |

---

# 7. Watershed Perimeter

Subbasin perimeters were not summed because adjacent subbasins share internal boundaries.

Instead, the subbasins were dissolved into a single watershed boundary.

The perimeter was then calculated from the dissolved geometry.

The perimeter in metres was converted to kilometres using:

$$
P_{km}=\frac{P_m}{1000}
$$

The resulting watershed perimeter was:

**2,305.48 km**

---

# 8. Elevation Analysis

Elevation statistics were calculated using **QGIS Zonal Statistics**.

The final fixed watershed/subbasin geometry was used as the zone layer, while the processed SRTM DEM was used as the raster layer.

The following statistics were obtained:

* Count
* Sum
* Mean
* Minimum
* Maximum

---

## 8.1 Mean Elevation

The watershed-wide mean elevation was calculated using the raster-cell sums and counts:

$$
\bar{E}=
\frac{\sum E_i}{N}
$$

where:

* \(E_i\) = elevation value of each valid raster cell
* \(N\) = number of valid raster cells

The total elevation sum was:

**13,495,200,689.12**

The total valid cell count was:

**24,424,448**

Therefore:

$$
\bar{E}=
\frac{13,495,200,689.12}{24,424,448}
$$

$$
\bar{E}=552.53\;m
$$

---

## 8.2 Elevation Range

The watershed-wide minimum and maximum elevations were:

* Minimum = **334.07 m**
* Maximum = **1,568.76 m**

---

## 8.3 Relief

Relief was calculated as:

$$
R=E_{max}-E_{min}
$$

Therefore:

$$
R=1,568.76-334.07
$$

$$
R=1,234.69\;m
$$

---

# 9. Slope Analysis

Slope was derived from the reprojected 30 m SRTM DEM using QGIS.

The resulting slope raster was subsequently clipped to the final watershed boundary.

The watershed-specific slope raster was:

```text
Hadejia_Slope_Watershed.tif
```

The clipped raster had a maximum slope of approximately:

**47.30°**

The original working slope raster extended beyond the final watershed and had a higher raster-wide maximum of approximately **51.52°**. The watershed-specific analysis uses the clipped raster.

---

## 9.1 Slope Classification

For the final map, slope was classified into three categories:

| Slope Range | Class          |
| ----------- | -------------- |
| 0–5°        | Low Slope      |
| 5–10°       | Moderate Slope |
| >10°        | Steep Slope    |

These thresholds were selected for this project to provide a clear hydrological representation of terrain and are not presented as universal hydrological standards.

---

## 9.2 Area-Weighted Mean Slope

The area-weighted mean slope was derived from QGIS zonal statistics.

The general calculation is:

$$
\bar{S}_w=
\frac{\sum_{i=1}^{n}S_iA_i}
{\sum_{i=1}^{n}A_i}
$$

where:

* \(S_i\) = mean slope of subbasin \(i\)
* \(A_i\) = area of subbasin \(i\)

The resulting area-weighted mean slope used in the project was:

**2.16°**

This value was calculated from QGIS-derived statistics and then incorporated into the integrated hydrological analysis.

---

# 10. Drainage-Network Analysis

The drainage network used in this analysis was derived by QSWAT from the processed DEM and watershed delineation settings.

The network contained **10 mapped reaches**.

The length of each reach was obtained from the QSWAT-derived drainage layer.

---

## 10.1 Total Drainage Length

The total mapped drainage length was calculated by summing the lengths of all mapped reaches:

$$
L_{total}=\sum_{i=1}^{n}L_i
$$

The total mapped length was:

$$
L_{total}=791,957.5\;m
$$

Converting to kilometres:

$$
L_{total}=
\frac{791,957.5}{1000}
$$

$$
L_{total}=791.96\;km
$$

---

# 11. Stream Order

Stream-order values were taken from the QSWAT-derived drainage network.

The mapped network contained:

* 6 first-order reaches
* 2 second-order reaches
* 2 third-order reaches

The highest mapped stream order was:

**3rd order**

The stream-order values represent the QSWAT-derived `strmOrder` attribute and were not independently recalculated using a separate manual Strahler-order procedure.

---

# 12. Drainage Density

Mapped drainage density was calculated using:

$$
D_d=\frac{L}{A}
$$

where:

* \(D_d\) = mapped drainage density (km/km²)
* \(L\) = total mapped drainage length (km)
* \(A\) = watershed area (km²)

Using the project values:

$$
D_d=
\frac{791.96}{22,619.66}
$$

$$
D_d=0.0350\;km/km^2
$$

The result is reported as **mapped drainage density** because the drainage length comes from the QSWAT-derived network.

---

# 13. Mapped Reach Frequency

Mapped reach frequency was calculated as:

$$
F_s=\frac{N}{A}
$$

where:

* \(F_s\) = mapped reach frequency
* \(N\) = number of mapped reaches
* \(A\) = watershed area

Using:

$$
N=10
$$

and:

$$
A=22,619.66\;km^2
$$

gives:

$$
F_s=
\frac{10}{22,619.66}
$$

$$
F_s=0.000442\;reaches/km^2
$$

This metric refers specifically to the mapped QSWAT-derived reaches.

---

# 14. Compactness Coefficient

The compactness coefficient was calculated using watershed perimeter and area.

The formula used was:

$$
K_c=
\frac{P}{2\sqrt{\pi A}}
$$

where:

* \(K_c\) = compactness coefficient
* \(P\) = watershed perimeter (km)
* \(A\) = watershed area (km²)

Using:

$$
P=2,305.48\;km
$$

and:

$$
A=22,619.66\;km^2
$$

gives:

$$
K_c\approx4.32
$$

The compactness coefficient is reported as a descriptive morphometric measure and is not assigned a qualitative ranking in this project.

---

# 15. Land-Cover Analysis

ESA WorldCover 2021 was used to characterize land cover within the final watershed.

The raster was prepared and clipped to the watershed boundary in QGIS.

The following classes were represented:

* Tree cover
* Shrubland
* Grassland
* Cropland
* Built-up
* Bare / sparse vegetation
* Permanent water bodies
* Herbaceous wetland

The land-cover analysis was descriptive and focused on spatial representation rather than modelling land-cover effects on runoff.

---

# 16. Streamflow Data Preparation

The Wudil daily streamflow dataset was processed in Python.

The original GRDC `.day` file contains daily observations.

The data were read using:

```python
data = pd.read_csv(
    path,
    sep=r"\s+",
    skiprows=5,
    header=None,
    names=["year", "month", "day", "hour", "minute", "discharge"]
)
```

The GRDC missing-value code was replaced with a null value:

```python
data["discharge"] = data["discharge"].replace(-9999.0, pd.NA)
```

A date field was created:

```python
data["date"] = pd.to_datetime(
    data[["year", "month", "day"]]
)
```

The study period was then selected:

```python
study_data = data[
    (data["date"] >= "1976-01-01") &
    (data["date"] <= "1990-12-31")
].copy()
```

The resulting dataset contained:

**5,479 daily observations**

with no missing discharge values after quality control.

---

# 17. Daily Streamflow Analysis

The daily observations were plotted as a time series to visualize discharge variability throughout the study period.

The daily hydrograph was used to examine:

* Short-term discharge variability
* High-flow events
* Low-flow periods
* Seasonal behaviour
* Year-to-year variation

The analysis does not assign causes to individual discharge events because rainfall and other forcing variables were not included in this project.

---

# 18. Monthly Mean Streamflow

Daily discharge was aggregated to monthly mean discharge using:

```python
monthly_flow = (
    study_data
    .set_index("date")["discharge"]
    .resample("ME")
    .mean()
)
```

This produced **180 monthly values** for the 15-year study period.

Monthly means were used to visualize the temporal structure of streamflow within each year.

---

# 19. Annual Mean Streamflow

Daily discharge was aggregated to annual mean discharge using:

```python
annual_flow = (
    study_data
    .set_index("date")["discharge"]
    .resample("YE")
    .mean()
)
```

Annual means were used for:

* Year-to-year comparison
* Annual variability analysis
* Linear trend analysis

---

# 20. Flow Duration Curve

The Flow Duration Curve (FDC) was constructed by sorting daily discharge values from highest to lowest.

```python
flow_sorted = np.sort(
    study_data["discharge"].values
)[::-1]
```

An exceedance probability was calculated using:

$$
P_e=
\frac{m}{n+1}\times100
$$

where:

* \(m\) = rank of the discharge value after sorting
* \(n\) = total number of observations
* \(P_e\) = exceedance probability (%)

The calculation was implemented as:

```python
exceedance_probability = (
    np.arange(1, len(flow_sorted) + 1)
    / (len(flow_sorted) + 1)
) * 100
```

The resulting FDC was used to obtain:

* **Q10 = 95.9 m³/s**
* **Q50 = 14.0 m³/s**
* **Q90 = 1.9 m³/s**

Q10 is the discharge exceeded approximately 10% of the time, Q50 is the median discharge, and Q90 is the discharge exceeded approximately 90% of the time.

---

# 21. High- and Low-Flow Thresholds

The 90th and 10th percentiles of the daily discharge distribution were used as descriptive thresholds.

The calculations were:

```python
high_flow_threshold = np.percentile(
    study_data["discharge"], 90
)

low_flow_threshold = np.percentile(
    study_data["discharge"], 10
)
```

The resulting thresholds were:

* High-flow threshold = **95.94 m³/s**
* Low-flow threshold = **1.92 m³/s**

The number of days meeting each condition was then calculated:

```python
high_flow_days = (
    study_data["discharge"] >= high_flow_threshold
).sum()

low_flow_days = (
    study_data["discharge"] <= low_flow_threshold
).sum()
```

Results:

* High-flow days = **552**
* Low-flow days = **555**

These thresholds are distribution-based descriptors of the observed record.

---

# 22. Recorded Zero-Discharge Days

The number of daily observations equal to zero was calculated from the cleaned discharge series.

The project identified:

**60 recorded zero-discharge days**

The proportion was calculated as:

$$
P_0=
\frac{N_0}{N}\times100
$$

where:

* \(N_0\) = number of zero-discharge observations
* \(N\) = total observations

Therefore:

$$
P_0=
\frac{60}{5479}\times100
$$

$$
P_0\approx1.10\%
$$

The remaining approximately **98.90%** of observations were non-zero.

These are reported as **recorded zero-discharge days** rather than independently verified physical zero-flow conditions.

---

# 23. Monthly Streamflow Climatology

Monthly climatology was calculated by grouping all daily observations according to calendar month across the full study period.

The calculation was implemented using:

```python
monthly_climatology = (
    study_data
    .set_index("date")["discharge"]
    .groupby(lambda x: x.month)
    .mean()
)
```

The resulting monthly climatology was:

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

---

# 24. Annual Coefficient of Variation

The coefficient of variation was calculated separately for each year using the standard deviation and mean of the daily discharge observations.

The calculation was:

$$
CV(\%)=
\frac{\sigma}{\bar{Q}}\times100
$$

where:

* \(\sigma\) = annual standard deviation of daily discharge
* \(\bar{Q}\) = annual mean discharge

The Python implementation was:

```python
annual_cv = (
    study_data
    .set_index("date")["discharge"]
    .resample("YE")
    .agg(["mean", "std"])
)

annual_cv["cv_percent"] = (
    annual_cv["std"] /
    annual_cv["mean"]
) * 100
```

The annual CV values ranged from:

**98.01% to 198.46%**

This indicates substantial within-year variability in the daily discharge observations.

---

# 25. Streamflow Trend Analysis

The temporal trend in annual mean discharge was assessed using simple linear regression.

The annual mean series was used rather than the complete daily series so that the trend analysis focused on changes in annual mean streamflow.

The regression was calculated using:

```python
from scipy.stats import linregress

years = annual_flow.index.year
annual_values = annual_flow.values

trend = linregress(
    years,
    annual_values
)
```

The fitted trend values were calculated as:

$$
Q_t=a+bt
$$

where:

* \(Q_t\) = fitted annual mean discharge
* \(a\) = intercept
* \(b\) = slope
* \(t\) = year

The fitted values were generated using:

```python
trend_values = (
    trend.intercept +
    trend.slope * years
)
```

---

## 25.1 Regression Results

| Metric    |                Result |
| --------- | --------------------: |
| Slope     | **−0.2098 m³/s/year** |
| Intercept |     **451.2557 m³/s** |
| R²        |            **0.0029** |
| p-value   |            **0.8485** |

The slope indicates the direction and fitted rate of the linear relationship.

The coefficient of determination was calculated as:

$$
R^2=r^2
$$

where \(r\) is the correlation coefficient returned by the linear regression.

The resulting:

$$
R^2=0.0029
$$

indicates that the fitted linear relationship explains very little of the variation in annual mean discharge.

The p-value of:

$$
p=0.8485
$$

does not provide statistical evidence of a detectable linear trend over the **1976–1990** study period.

---

# 26. Integrated Hydrological Interpretation

The final interpretation combines:

### Physical watershed characteristics

* Area
* Perimeter
* Elevation
* Relief
* Mean slope
* Drainage length
* Drainage density
* Stream order
* Land cover

### Observed hydrological behaviour

* Mean discharge
* Median discharge
* Flow-duration characteristics
* Seasonal variation
* High- and low-flow conditions
* Zero-discharge observations
* Annual variability
* Linear trend

The two components are interpreted together to describe the watershed's physical setting and the temporal behaviour of observed discharge.

The analysis does not attempt to establish causal relationships between rainfall, land cover, terrain, groundwater, and streamflow because those relationships were not explicitly modelled.

---

# 27. Summary of Main Calculations

| Parameter                    | Calculation                     |                   Result |
| ---------------------------- | ------------------------------- | -----------------------: |
| Watershed area               | \(\sum A_i / 1,000,000\)        |        **22,619.66 km²** |
| Watershed perimeter          | \(P_m/1000\)                    |          **2,305.48 km** |
| Mean elevation               | \(\sum E_i/N\)                  |             **552.53 m** |
| Relief                       | \(E_{max}-E_{min}\)             |           **1,234.69 m** |
| Mean slope                   | Area-weighted zonal mean        |                **2.16°** |
| Total mapped drainage length | \(\sum L_i/1000\)               |            **791.96 km** |
| Drainage density             | \(L/A\)                         |        **0.0350 km/km²** |
| Reach frequency              | \(N/A\)                         | **0.000442 reaches/km²** |
| Compactness coefficient      | \(P/[2\sqrt{\pi A}]\)           |                 **4.32** |
| Q10                          | 90th-discharge percentile / FDC |            **95.9 m³/s** |
| Q50                          | Median / FDC                    |            **14.0 m³/s** |
| Q90                          | 10th-discharge percentile / FDC |             **1.9 m³/s** |
| Zero-discharge proportion    | \(60/5479\times100\)            |                **1.10%** |
| Trend slope                  | Linear regression               |    **−0.2098 m³/s/year** |
| Trend R²                     | \(r^2\)                         |               **0.0029** |

---

# 28. Software and Analytical Environment

The project used:

* **QGIS** for GIS processing, terrain analysis, spatial calculations, and map production.
* **QSWAT** for watershed and subbasin delineation and drainage-network generation.
* **Python** for observed streamflow processing and statistical analysis.
* **Jupyter Notebook** for reproducible hydrological calculations, plots, and interpretation.
* **Pandas** for time-series processing.
* **NumPy** for numerical calculations.
* **SciPy** for linear regression.
* **Matplotlib** for visualization.

The workflow intentionally separates spatial watershed analysis from observed streamflow analysis while integrating their results in the final interpretation.
