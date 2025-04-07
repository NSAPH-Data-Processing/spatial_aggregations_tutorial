# What is Rasterization?

Rasterization is the process of converting vector data (such as shapes, polygons, or lines defined by mathematical formulas) into raster data, which consists of a grid of cells or pixels. Rasterization is one of two kinds of GIS (geographic information systems) data conversions (the other being _vectorization_).

Put simply, rasterization is the process of turning the shapes or objects (like triangles or polygons) from an image into a grid of tiny squares called pixels. Imagine drawing a triangle on graph paper: rasterization figures out which squares the triangle covers and fills them with color. This makes it possible to display the triangle as an image on a screen or use it in analysis.

A good video visualizing this is here: [DATA CONVERSION IN GIS- RASTERIZATION & VECTORIZATION](https://www.youtube.com/watch?v=8aU031e1KiY)

# So why should I care about rasterization?
Rasterization is commonly used in GIS applications because it allows for the integration of vector data with raster data, enabling a wide range of spatial analyses:
* **Analysis** Raster data is often easier to work with for certain types of calculations, such as overlay analysis, interpolation, and modeling continuous phenomena (like temperature or pollution levels). 
* **Visualization** Rasterization also allows for the visualization of complex vector data in a format that can be easily interpreted and analyzed.
* **Computationla efficiency** Raster data is often more efficient for large datasets, as it can be processed in parallel and requires less memory than vector data.
* **Spatial aggregations** It is and intermediate step that allows for spatial aggregation of vector data, which is essential for environmental health research and other applications where understanding spatial patterns is crucial.

For more information on vectors vs. rasters, see [this post](https://github.com/NSAPH-Data-Processing/spatial_aggregations_tutorial/blob/main/vector_vs_raster.md)!

# Wait, vectorization…?

Vectorization which consists of converting raster data (like images or grids) into vector data (like points, lines, or polygons). 

Vector data is more precise and can represent complex shapes, while raster data is simpler and better suited for continuous data (like temperature or elevation). Nonetheless, in general, conversion from vector to raster is more common than the reverse, and is computationally complex and less accurate 

## Rasterization Approaches

Assigning a cell grid to a polygon is basically a cookie cutter operation, where the cell coordinates that overlap with a polygon are identified.

While straightforward, there are some small differences in the specific approach used, leading to different outcomes. Mainly two different approaches to consider:

* **All touched approach**
   - Assigns the value of the polygon to all cells that are touched by the polygon, even if only a small part of the cell is covered.
   - This approach is useful when you want to ensure that all areas within the polygon are represented in the raster, even if they only partially overlap with the cells.
* **Intersection approach**
   - Assigns the value of the polygon to only those cells that are fully contained within the polygon.
   - This approach is useful when you want to ensure that only the areas that are completely covered by the polygon are represented in the raster.
   - This can lead to missing values in the raster if the polygon does not fully cover any cells.
* **Cell center approach**
   - Assigns the value of the polygon to only those cells whose center point is within the polygon.
   - This approach is useful when you want to ensure that only the areas whose center falls in the polygon are represented in the raster.
   - This can lead to missing values in the raster if the polygon does not fully cover center cells.

### Pros of All Touched

* More inclusive: Useful when you don’t want to miss anything — e.g., when calculating population exposure or coverage.

* Better for aggregating data: Reduces the chance of underestimating areas that barely touch a polygon (like census tracts or ZIP codes).

### Cons of All Touched

* May overestimate area: Because it includes partially touched cells, the polygon can appear larger in the raster than it actually is.

* Lower spatial precision: Especially at coarser resolutions, this can introduce more noise.

> **Example Use Case** In environmental health, where you want to capture all areas that may be influenced by a pollution source, all touched is often preferred — better to slightly overestimate exposure than to miss out on parts of a population.


## Python Implementation

```{python}
import rasterio
from rasterio import features
import geopandas as gpd

# Load vector data
vector = gpd.read_file("path/to/vector.shp")

# Create a raster template
with rasterio.open("path/to/reference_raster.tif") as src:
    raster_template = src.meta.copy()

# Rasterize vector data
rasterized = features.rasterize(
    [(geometry, value) for geometry, value in zip(vector.geometry, vector['attribute'])],
    out_shape=src.shape,
    transform=src.transform,
    fill=0,
    all_touched=True,
    dtype='float32'
)

# Save the rasterized data
with rasterio.open("output_raster.tif", "w", **raster_template) as dst:
    dst.write(rasterized, 1)
```

## R Implementation

```{r}
library(raster)
library(sf)

# Create a raster template
r <- raster(ncols=36, nrows=18)

# Create random points
set.seed(123)
n <- 1000
x <- runif(n) * 360 - 180
y <- runif(n) * 180 - 90
xy <- cbind(x, y)

# Rasterize points
r1 <- rasterize(xy, r, field=1)
r2 <- rasterize(xy, r, fun=function(x,...)length(x))

# Plot the results
plot(r1)
plot(r2)
```

> # Rasterization Strategies
> In addition to the basic rasterization methods, there are several advanced strategies for rasterizing vector data, each with its own strengths and applications in environmental health research. Here are some of the most common strategies:
>1. **Basic Grid-Based Rasterization**: Divides the study area into a uniform grid of cells, assigning values based on the presence or attributes of vector features within each cell. This is widely used for environmental exposure modeling and landscape metrics[^13][^15].
> 2. **Proximity-Based Rasterization**: Converts vector data into rasters by calculating distances from features (e.g., pollution sources, rivers) and assigning values based on proximity. This is useful for exposure assessments and susceptibility mapping[^20].
> 3. **Weighted Aggregation**: Rasterizes vector data by assigning weights to features (e.g., land use types or pollution levels) and aggregating them into raster cells. This supports risk stratification and environmental health evaluations[^18][^19].
> 4. **Interpolation-Based Rasterization**: Uses interpolation methods to convert sparse vector points (e.g., monitoring stations) into continuous raster surfaces, such as air quality or temperature maps[^15][^20].
> 5. **Multi-Layer Integration**: Combines multiple rasterized datasets (e.g., climate, socioeconomic, and pollution layers) to model complex environmental health interactions[^15][^17].
> 6. **Machine Learning Approaches**: Employs machine learning algorithms to predict raster values based on vector features and other covariates, enhancing exposure assessments and risk modeling[^15][^19].
> 7. **Statistical Rasterization**: Applies statistical methods to rasterize vector data, enabling the estimation of uncertainty and variability in environmental health assessments. It can incorporate time as a variable in rasterization, allowing for dynamic modeling of environmental changes and health impacts over time

- [^13]: [A Comparison of Vector and Raster GIS Methods - USDA](https://research.fs.usda.gov/treesearch/6065)
- [^15]: [A review of geospatial exposure models - Nature](https://www.nature.com/articles/s41370-024-00712-8)
- [^17]: [Children's Environmental Health Indicators - Vital Strategies](https://www.vitalstrategies.org/childrens-environmental-health-indicators/)
- [^18]: [Optimization strategy for community planning - Frontiers](https://www.frontiersin.org/journals/public-health/articles/10.3389/fpubh.2024.1347122/full)
- [^19]: [Statistical Methods for Linking Health, Exposure, and Hazards - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC1247575/)
- [^20]: [Using Geographic Information Systems for Exposure Assessment - EHP](https://ehp.niehs.nih.gov/doi/full/10.1289/ehp.6738)
