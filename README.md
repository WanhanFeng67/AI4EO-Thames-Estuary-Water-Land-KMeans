# Water–Land Boundary Mapping and Water Extent Comparison in the Thames Estuary Using Sentinel-2 Imagery and K-means Clustering

## 1. Project Overview

This project applies a lightweight AI for Earth Observation (AI4EO) workflow to map water–land boundaries in the Thames Estuary, UK. The workflow uses Sentinel-2 satellite imagery from Google Earth Engine, calculates the Normalized Difference Water Index (NDWI), and applies K-means clustering to classify the study area into water and land.

The project also compares classified water extent between 2020 and 2024. This comparison is treated as a water extent comparison rather than definitive coastline change detection, because estuarine environments are strongly influenced by tidal conditions, exposed mudflats, and image acquisition timing.

---

## 2. Problem Background

Water–land boundary mapping is important for coastal monitoring, flood-risk assessment, estuarine management, and sea-level rise adaptation. The Thames Estuary is a dynamic coastal and estuarine environment in southeast England, where the boundary between water and land can vary due to tides, mudflat exposure, and seasonal hydrological conditions.

Traditional coastline or water boundary mapping can require manual interpretation or detailed ground-based surveys. Remote sensing provides a repeatable and scalable way to monitor water extent over time. In this project, K-means clustering is used as an unsupervised machine learning method to separate water and land using NDWI values, without requiring manually labelled training data.

---

## 3. Study Area

The study area is located in the Thames Estuary, southeast England. The selected region includes estuarine water bodies, coastal land, mudflats, and surrounding urban and agricultural areas.

The study area was defined using the following bounding box:

```python
region = ee.Geometry.Rectangle([0.55, 51.40, 1.05, 51.65])
```
This area was selected because the Thames Estuary contains a clear water–land boundary and is environmentally relevant for coastal monitoring and flood-risk management.

## 4. Data

The project uses Sentinel-2 Surface Reflectance imagery accessed through Google Earth Engine.

| Item | Description |
|---|---|
| Dataset | Sentinel-2 Surface Reflectance Harmonized |
| Google Earth Engine collection | `COPERNICUS/S2_SR_HARMONIZED` |
| Study years | 2020 and 2024 |
| Season | June–September |
| Cloud filter | Less than 20% cloud cover |
| Bands used | B3 Green and B8 Near Infrared |
| Index calculated | NDWI |

NDWI was calculated as:

```text
NDWI = (Green - NIR) / (Green + NIR)
```

For Sentinel-2:

```text
Green = B3
NIR = B8
```

NDWI is useful for water detection because water bodies usually have higher NDWI values than land surfaces.

## 5. Methodology

The project follows three main workflow stages:

1. Remote sensing data preparation
2. K-means water-land classification
3. 2020–2024 water extent comparison

### 5.1 Remote Sensing Workflow

![Remote sensing workflow](<Remote sensing workflow diagram.png>)

Sentinel-2 imagery was accessed through Google Earth Engine. Images were filtered by date, location, and cloud cover. A summer median composite was created for each study year to reduce noise and improve spatial coverage. NDWI was then calculated from the Green and NIR bands.

The NDWI calculation is:

```text
NDWI = (B3 - B8) / (B3 + B8)
```

where B3 is the green band and B8 is the near-infrared band in Sentinel-2 imagery.

### 5.2 K-means Classification Workflow

![K-means workflow](<K-means classification workflow diagram.png>)

K-means clustering was applied to the NDWI image using two clusters. The cluster with the higher mean NDWI value was interpreted as water, while the cluster with the lower mean NDWI value was interpreted as land.

The basic classification logic is:

```text
Higher mean NDWI cluster = water
Lower mean NDWI cluster = land
```

This allows the model to classify the study area into water and land without manually labelled training data.

### 5.3 Water Extent Comparison Workflow

![Water extent comparison workflow](<Water extent comparison workflow diagram.png>)

The water masks from 2020 and 2024 were compared to identify classified water gain and water loss.

```text
Water gain = non-water in 2020 → water in 2024
Water loss = water in 2020 → non-water in 2024
```

This comparison was used to produce a water extent change map. However, the result should be interpreted carefully because the Thames Estuary is affected by tidal conditions, exposed mudflats, and satellite image acquisition timing.

---

## 6. Results

### 6.1 Sentinel-2 RGB Composite, 2024

![Sentinel-2 RGB 2024](<Sentinel-2 RGB Composite of the Thames Estuary (2024).png>)

This figure shows the Sentinel-2 true-colour composite of the Thames Estuary in 2024. It provides the original remote sensing context before applying NDWI and K-means classification.

### 6.2 NDWI Map, 2024

![NDWI 2024](<NDWI Map of the Thames Estuary (2024).png>)

This figure shows the NDWI map derived from the Sentinel-2 Green and NIR bands. Higher NDWI values highlight water bodies, while lower values generally correspond to land surfaces.

### 6.3 K-means Water-Land Classification, 2024

![K-means water-land comparison](<K-means Water-Land Classification (2024).png>)

This figure shows the final K-means water-land classification for 2024. Blue represents water and green represents land. The boundary between the two classes represents the detected water-land boundary.

### 6.4 NDWI Threshold vs K-means Water Mask, 2024

![NDWI threshold vs K-means](<NDWI Threshold vs K-means Water Mask (2024).png>)

This figure compares the K-means water mask with a simple NDWI threshold method. The threshold method acts as a baseline comparison to visually assess whether the unsupervised K-means classification produces a reasonable water mask.

### 6.5 K-means Water-Land Classification Comparison, 2020–2024

![2020 2024 K-means comparison](<K-means Water-Land Classification comparison （2020-2024）.png>)

This figure compares the K-means water-land classification results for 2020 and 2024. It provides a visual comparison of classified water extent across two time periods.

### 6.6 Detected Water Extent Change, 2020–2024

![Water gain loss](<Detected Water Extent Change (2020–2024).png>)

This figure shows detected water extent change between 2020 and 2024. Blue areas indicate pixels classified as water in 2024 but not in 2020, while red areas indicate pixels classified as water in 2020 but not in 2024.

This result should be interpreted as a classified water extent comparison rather than definitive shoreline change, because the Thames Estuary is affected by tidal conditions, exposed mudflats, seasonal variation, and image acquisition timing.

---

## 7. Quantitative Area Results

After generating the K-means water and land masks, the area of each class was calculated using the pixel area of the classified image.

| Year | Water area km² | Land area km² |
|---|---:|---:|
| 2020 | 515.32 | 449.88 |
| 2024 | 474.18 | 491.02 |
| Difference, 2024 - 2020 | -41.14 | 41.14 |

The area calculation shows that the classified water area decreased from 515.32 km² in 2020 to 474.18 km² in 2024, while the classified land area increased from 449.88 km² to 491.02 km². This indicates a net decrease of 41.14 km² in classified water extent within the selected study area.

However, these values should be interpreted carefully. Because the Thames Estuary is strongly affected by tides, exposed mudflats, seasonal variation, and satellite acquisition timing, the area difference should be understood as a comparison of classified water extent between two image composites rather than definitive long-term shoreline change.

---

## 8. Discussion and Limitations

This project demonstrates a simple unsupervised AI4EO workflow for water-land boundary mapping. The method is computationally lightweight and does not require manually labelled training data.

However, there are several limitations:

- The method does not use ground-truth shoreline or water boundary data.
- Classification accuracy is assessed mainly through visual interpretation and comparison with an NDWI threshold method.
- The Thames Estuary is affected by tidal conditions, which can change visible water extent between satellite acquisition times.
- Mixed pixels near the water-land boundary may reduce classification accuracy.
- The output is a pixel-based water-land classification map rather than a precise vector coastline.
- The 2020–2024 comparison should not be interpreted as definitive long-term coastline change.

Future work could include comparing results with official coastline datasets, adding tidal stage information, testing other indices such as MNDWI, or using supervised classification with labelled validation data.

---

## 9. Environmental Cost Assessment

The environmental cost of this project is relatively low because it uses open-access Sentinel-2 imagery and a lightweight unsupervised machine learning method.

The main computational costs come from:

- Accessing Sentinel-2 imagery through Google Earth Engine
- Creating summer median composites
- Calculating NDWI
- Sampling pixels for K-means clustering
- Running K-means classification
- Exporting and visualising results

Compared with deep learning approaches, this workflow does not require GPU training, large model optimisation, or repeated training of complex neural networks. To reduce computational cost, the analysis uses a limited study area, two seasonal composites, and 5,000 sampled pixels for K-means clustering.

Overall, this project represents a low-computation AI4EO workflow suitable for rapid environmental monitoring.

---

## 10. Acknowledgement

This project builds on tools and methods covered in the AI4EO course, including Google Earth Engine data access, Sentinel-2 image processing, NDWI calculation, and unsupervised K-means clustering. The project adapts these tools to a new environmental monitoring problem: water-land boundary mapping and water extent comparison in the Thames Estuary.
