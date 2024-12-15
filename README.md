# Time Series Data Analysis for Field Boundary Identification and Field Acreage Calculation

This repository applies a **Time Series Data Analysis** approach to **Sentinel-2 satellite imagery**, identifying agricultural fields by crop type and their boundaries, and calculating the number and acreage of these fields. While using time series data provides more robust and reliable results, the algorithm also efficiently analyzes single images.
## Method

1. **Data Loading and Resampling**:  
   The algorithm loads individual Sentinel-2 bands from GeoTIFF files, applies a spatial transformation, and resamples the data to a specified resolution and extent.

2. **Masking**:  
   A valid mask based on the "SCL" (Scene Classification Layer) band is applied. Only pixels labeled as 'Vegetation' (class 4) and 'Cloud Shadows' (class 3) are considered. Other pixels are masked out and set to NaN.

3. **Data Aggregation**:  
   The algorithm combines data from multiple months. For each pixel, the algorithm calculates the mean value across months, considering only the valid (non-masked) pixels.

4. **PCA and Clustering**:  
   - **PCA (Principal Component Analysis)**: PCA is applied to the aggregated mean bands, reducing the data's dimensionality to three components. This step extracts the most significant features of the data while discarding less important variance.
   - **K-means Clustering**: K-means clustering is performed on the PCA-transformed data. The **elbow method** is used to autonatically determine the optimal number of clusters by calculating inertia for various numbers of clusters and identifying the "elbow" point, where inertia decreases more slowly.

5. **Polygon Creation**:  
   - The clustering labels are converted into spatial polygons.
   - The polygons undergo cleaning operations, such as filling holes and smoothing boundaries.
   - A GeoDataFrame is created containing the valid polygons (fields), with a minimum area threshold applied to filter out small polygons and ensure that only valid geometries are kept.

6. **Field Statistics and Visualization**:  
   - Statistics are calculated for each crop type, including the number of fields and their acreage.
   - Visualizations are generated to display the identified clusters and fields, and to compare the fields and their boundaries with **True Color Imagery** and **ground truth** data.

This analysis uses imagery, shapefiles, and ground truth data from **Huron County (Michigan)**.

## Sentinel-2 Data

Sentinel-2 data is available through AWS (no AWS account required):  
[Sentinel-2 L2A COGS](https://registry.opendata.aws/sentinel-2-l2a-cogs/)

For this analysis, tile **17TLJ** was used, but other tiles can also be utilized.

## Data Folder Structure

The tile data folder structure mimics the Sentinel-2 folder structure and naming conventions (Year/month_number/code_for_day_of_shooting). Example: 2022/6/S2A_17TLJ_20220628_0_L2A

## Shapefile and Ground Truth Data

The shapefile and ground truth data are sourced from the **CropScape - Cropland Data Layer** project by the **George Mason University Center for Spatial Information Science and Systems**:  
[CropScape - Cropland Data Layer](https://nassgeodata.gmu.edu/CropScape/)

The paths to the data folder, shapefile, and ground truth data are defined in the "Data Paths and Parameters" block. The default paths are as follows:

```python
data_folder = "/data/2022"
county_shapefile = "/data/shp_gmu/26063.shp"
ground_truth_path = os.path.join(data_folder, "cdl_2022.tif")
```

Adjust these paths as needed based on your local setup.
