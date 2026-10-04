# Sentinel-1 Soil Moisture Retrieval in Google Earth Engine

This repository contains a **Google Earth Engine (GEE)** workflow for estimating and monitoring soil moisture using **Sentinel-1 SAR** observations.

The workflow is applied over the **Montemarano33N study area in southern Italy** and provides spatial soil-moisture maps as well as temporal variations at selected observation sites.

## Overview

The workflow includes:

* Definition of the study region and monitoring sites
* Sentinel-1 SAR data filtering
* Selection of **VV polarization** and **Interferometric Wide (IW)** mode
* Separation of **ascending and descending orbits**
* Generation of **10-day temporal composites**
* Conversion from Sentinel-1 backscatter in dB to linear sigma⁰
* Spatial speckle filtering
* Water and urban-area masking
* Relative soil-moisture estimation using minimum–maximum normalization
* Scaling to an absolute soil-moisture range of **0–0.6 m³/m³**
* Spatial visualization of mean soil moisture
* Temporal soil-moisture analysis at six selected sites
* Export of site-level soil-moisture time series as CSV files

## Study Area

The analysis is conducted over the **Montemarano33N** region in Italy.

Six monitoring sites are defined within the study area for extracting soil-moisture time series.

The approximate coordinates of the monitoring sites are provided directly in the Google Earth Engine script.

## Data

### Sentinel-1

The workflow uses Sentinel-1 SAR observations with the following filters:

* Polarization: **VV**
* Acquisition mode: **IW**
* Orbit direction: **Ascending and Descending**
* Analysis period: **1 January 2025 – 31 December 2025**

The Sentinel-1 observations are aggregated into **10-day composites** separately for ascending and descending acquisitions.

## Processing Workflow

### 1. Study Region

The study region is imported as a Google Earth Engine `FeatureCollection`:

```javascript
var regionFC = ee.FeatureCollection(
  'projects/sentinell1/assets/Montemarano33N'
);
```

The region and monitoring sites are displayed on the map.

### 2. Sentinel-1 Filtering

Sentinel-1 observations are filtered according to:

```text
Time period
      ↓
Study region
      ↓
VV polarization
      ↓
IW acquisition mode
      ↓
Ascending / Descending orbit
```

### 3. Temporal Compositing

The available Sentinel-1 observations are aggregated into **10-day mean composites**.

This produces two image collections:

* `asc_10days` – ascending orbit
* `des_10days` – descending orbit

Only time intervals containing valid Sentinel-1 observations are retained.

### 4. Sigma⁰ Conversion and Speckle Filtering

Sentinel-1 backscatter is initially represented in dB.

The workflow converts the values to linear sigma⁰:

```text
σ⁰ = 10^(backscatter_dB / 10)
```

A spatial focal-mean filter with a **30 m × 30 m neighborhood** is then applied to reduce speckle effects.

### 5. Water and Urban Masks

Additional masks are applied to exclude water and urban areas from the soil-moisture estimation.

The masks are derived from the corresponding labeled image collection available in the GEE project.

### 6. Soil-Moisture Estimation

A relative soil-moisture index is calculated using the temporal minimum and maximum backscatter values:

```text
SM_relative = (σ⁰ - σ⁰_min) / (σ⁰_max - σ⁰_min)
```

The resulting normalized values are then scaled to an assumed absolute soil-moisture range:

```text
SM_min = 0.0 m³/m³
SM_max = 0.6 m³/m³
```

Therefore:

```text
SM_absolute =
SM_relative × (0.6 - 0.0) + 0.0
```

The procedure is applied independently to ascending and descending observations.

## Outputs

### Spatial Soil-Moisture Maps

The script generates mean soil-moisture maps for:

* **Ascending Sentinel-1 observations**
* **Descending Sentinel-1 observations**

The maps are displayed over the entire study region.

### Site-Level Time Series

Soil-moisture time series are extracted for six monitoring sites.

Separate time series are generated for:

* Ascending orbit
* Descending orbit

The resulting charts show soil moisture in **m³/m³** as a function of time.

### CSV Export

The script also allows the user to export a soil-moisture time series for an individual monitoring site.

Separate CSV files can be generated for ascending and descending observations.

For example:

```text
1_ASC.csv
1_DES.csv
```

The exported files contain:

```text
Date
Soil Moisture (m³/m³)
```

## Repository Structure

```text
.
├── README.md
└── sentinel1_soil_moisture.js
```

## Google Earth Engine

The analysis was implemented using **Google Earth Engine JavaScript API**.

The script can be copied into the GEE Code Editor after providing access to the required project assets.

## Notes

This workflow provides a **relative Sentinel-1-based soil-moisture retrieval that is subsequently scaled to a predefined 0–0.6 m³/m³ range**.

The resulting values should therefore be interpreted within the assumptions of the implemented normalization and scaling approach. For quantitative validation, the estimates should be compared against independent soil-moisture observations or established satellite soil-moisture products.

## Applications

This workflow can be used as a starting point for:

* Sentinel-1 soil-moisture monitoring
* Agricultural monitoring
* Temporal analysis of surface conditions
* SAR-based environmental monitoring
* Comparison of ascending and descending SAR observations
* Integration of SAR observations with ground measurements and other Earth-observation datasets

## Author

**Mina Rahmani**
PhD in Geodesy | GNSS-R & Earth Observation
University of Naples Federico II

---

*Developed using Google Earth Engine and Sentinel-1 SAR data.*
