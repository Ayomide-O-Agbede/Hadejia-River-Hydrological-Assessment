# Hadejia River Watershed Hydrological Assessment

## Overview

This project assesses the physical and hydrological characteristics of the **Hadejia River watershed in northern Nigeria** using GIS, QSWAT and Python.

The analysis combines:

1. **GIS-based watershed and terrain analysis** using QGIS/QSWAT.
2. **Observed streamflow analysis** using daily discharge data from the Wudil gauge and Python.

The project focuses on watershed characteristics, drainage structure, land cover, and the temporal behaviour of observed streamflow.

> **Note:** QSWAT was used for watershed delineation, subbasin generation and drainage-network extraction. A complete or calibrated SWAT rainfall-runoff simulation was not carried out.

## Objectives

The main objective was to assess the physical characteristics and observed streamflow behaviour of the Hadejia River watershed.

Specific objectives were to:

* Delineate the watershed and subbasins using a 30 m SRTM DEM.
* Analyse elevation, slope and drainage characteristics.
* Calculate selected watershed morphometric parameters.
* Map land cover using ESA WorldCover 2021.
* Analyse daily streamflow at the Wudil gauge from 1976–1990.
* Examine seasonal and annual streamflow variability.
* Assess flow-duration characteristics, high- and low-flow conditions, and recorded zero-discharge days.
* Assess the linear trend in annual mean streamflow.

## Study Area

The final watershed was delineated using QSWAT with the **Wudil gauge (GRDC Station 1837410)** as the outlet reference.

The final delineation produced:

* **10 subbasins**
* **22,619.66 km²** watershed area
* **2,305.48 km** watershed perimeter
* **791.96 km** of mapped drainage reaches
* Maximum mapped stream order of **3rd order**
* Elevation range of **334.07–1,568.76 m**
* Area-weighted mean slope of **2.16°**

The main spatial analysis was performed in **WGS 84 / UTM Zone 32N (EPSG:32632)**.

## Streamflow Data

Observed daily discharge data were obtained from the **GRDC Wudil gauge, Station 1837410**.

The selected analysis period was:

**1 January 1976 – 31 December 1990**

The processed record contains:

* **5,479 daily observations**
* Mean discharge: **35.32 m³/s**
* Median discharge: **14.00 m³/s**
* Maximum recorded discharge: **704.68 m³/s**
* Recorded zero-discharge days: **60**

## Key Results

### Watershed and terrain

| Parameter                |            Result |
| ------------------------ | ----------------: |
| Watershed area           | **22,619.66 km²** |
| Watershed perimeter      |   **2,305.48 km** |
| Mean elevation           |      **552.53 m** |
| Minimum elevation        |      **334.07 m** |
| Maximum elevation        |    **1,568.76 m** |
| Relief                   |    **1,234.69 m** |
| Area-weighted mean slope |         **2.16°** |
| Maximum watershed slope  |        **47.30°** |
| Mapped drainage length   |     **791.96 km** |
| Mapped drainage density  | **0.0350 km/km²** |
| Compactness coefficient  |          **4.32** |

### Streamflow

| Parameter                    |                Result |
| ---------------------------- | --------------------: |
| Mean discharge               |        **35.32 m³/s** |
| Median discharge             |        **14.00 m³/s** |
| Q10                          |         **95.9 m³/s** |
| Q50                          |         **14.0 m³/s** |
| Q90                          |          **1.9 m³/s** |
| High-flow threshold          |        **95.94 m³/s** |
| Low-flow threshold           |         **1.92 m³/s** |
| Recorded zero-discharge days |        **60 (1.10%)** |
| Annual CV range              |     **98.01–198.46%** |
| Trend slope                  | **−0.2098 m³/s/year** |
| R²                           |            **0.0029** |
| p-value                      |            **0.8485** |

The linear regression of annual mean discharge does not provide statistical evidence of a detectable linear trend over the 1976–1990 period.

## Maps

### Watershed Overview

![Watershed Overview](maps/Map_1_Watershed_Overview.jpeg)

### Subbasins and Drainage Network

![Subbasins and Drainage Network](maps/Map_2_Subbasins_Drainage_Network.jpeg)

### Slope

![Slope Map](maps/Map_3_Slope.jpeg)

### Land Cover

![Land Cover Map](maps/Map_4_Land_Cover.jpeg)

## Streamflow Figures

The Python analysis produced eight figures covering daily, monthly and annual streamflow behaviour, flow duration, monthly climatology, high- and low-flow conditions, annual variability and trend.

Files are available in the `figures/` folder:

* `Figure_1_Daily_Streamflow.png`
* `Figure_2_Monthly_Mean_Streamflow.png`
* `Figure_3_Annual_Mean_Streamflow.png`
* `Figure_4_Flow_Duration_Curve.png`
* `Figure_5_Monthly_Climatology.png`
* `Figure_6_High_Low_Flow_Days.png`
* `Figure_7_Annual_CV.png`
* `Figure_8_Annual_Streamflow_Trend.png`

## Main Tools

* **QGIS** — GIS processing, terrain analysis, spatial calculations and mapping
* **QSWAT** — watershed and subbasin delineation and drainage-network extraction
* **Python** — streamflow processing and statistical analysis
* **Jupyter Notebook** — hydrological analysis and figure generation
* **Pandas / NumPy** — data processing and numerical analysis
* **SciPy** — linear regression
* **Matplotlib** — visualization

## Repository Structure

```text
Hadejia-River-Watershed-Hydrological-Assessment/
│
├── README.md
│
├── DOCUMENTATION/
│   ├── Methodology.md
│   ├── Processing_Notes.md
│   └── Data_Sources.md
│
├── maps/
│   ├── Map_1_Watershed_Overview.jpeg
│   ├── Map_2_Subbasins_Drainage_Network.jpeg
│   ├── Map_3_Slope.jpeg
│   └── Map_4_Land_Cover.jpeg
│
├── figures/
│   ├── Figure_1_Daily_Streamflow.png
│   ├── Figure_2_Monthly_Mean_Streamflow.png
│   ├── Figure_3_Annual_Mean_Streamflow.png
│   ├── Figure_4_Flow_Duration_Curve.png
│   ├── Figure_5_Monthly_Climatology.png
│   ├── Figure_6_High_Low_Flow_Days.png
│   ├── Figure_7_Annual_CV.png
│   └── Figure_8_Annual_Streamflow_Trend.png
│
└── notebook/
    └── Hadejia_Streamflow_Hydrological_Analysis.ipynb
```

## Interpretation and Limitations

The results describe the physical watershed and the observed streamflow behaviour at the Wudil gauge. They should not be interpreted as a complete representation of hydrological conditions at every location within the watershed.

Important limitations include:

* HydroBASINS was used only as an initial spatial reference; the final boundary was delineated using QSWAT.
* The drainage network is a QSWAT-derived mapped network and does not represent every natural or artificial drainage feature.
* The land-cover dataset represents **2021**, while the streamflow analysis covers **1976–1990**.
* The streamflow analysis is based on one gauge location.
* Recorded zero-discharge values were retained but were not independently field-validated.
* The trend analysis is statistical and does not establish the causes of streamflow variation.
* Rainfall, evaporation, abstraction, reservoir operations and other possible controls were not jointly modelled.
* No calibrated rainfall-runoff simulation was performed.

For the full analytical procedures, processing decisions and data sources, see the files in `DOCUMENTATION/`.
