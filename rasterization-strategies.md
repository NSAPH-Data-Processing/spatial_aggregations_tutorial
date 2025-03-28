# What is Rasterization?

Rasterization is the process of converting vector data (such as shapes, polygons, or lines defined by mathematical formulas) into raster data, which consists of a grid of cells or pixels. Rasterization is one of two kinds of GIS (geographic information systems) data conversions (the other being _vectorization_).

### Main Applications of Rasterization in Environmental Health Data Science

1. **Environmental Monitoring and Modeling**:
   * Rasterization enables the integration of satellite imagery with vector data, such as habitat boundaries, to monitor ecosystem changes, deforestation, and wildfire spread[^1][^2].
   * It supports modeling continuous environmental phenomena like air pollution, temperature, and precipitation across landscapes[^1][^3].
2. **Exposure Assessment**:
   * Rasterized climate and pollution data are used to estimate individual or population-level exposure to environmental hazards (e.g., heat waves or air pollutants) by overlaying raster grids with demographic polygons[^4][^5].
3. **Urban Planning and Public Health**:
   * By converting land-use data into raster format, researchers can calculate runoff coefficients, assess habitat connectivity, and evaluate urban heat islands, aiding sustainable urban development[^1].
4. **Disaster Preparedness and Response**:
   * Raster data is crucial for tracking natural disasters like floods or hurricanes and assessing their health impacts by combining raster hazard maps with population density polygons[^6][^2].
5. **Agricultural and Food Security Studies**:
   * Rasterized soil temperature, crop health, and irrigation data help assess food security risks and optimize agricultural practices to mitigate environmental health impacts like malnutrition[^6].

# Wait, vectorization…?

A vector image is a type of digital graphic created using mathematical formulas to define geometric shapes, lines, curves, and colors. An example of a vector image is an SVG or PDF. Unlike raster images, which are composed of pixels, vector images rely on paths and control points to represent visual elements, meaning that the data embedded in a file of this type are geometric instructions. These instructions specify the position, size, shape, and other attributes of these elements within a Cartesian coordinate system, allowing the image to be rendered precisely and scaled without loss of quality. This is why SVG plots are appear super clear, and you can zoom into them almost infinitely without them getting blurry or blocky.

That being said, SVGs have numerous drawbacks:

### Drawbacks of Vector Images

1. **Limited Detail for Complex Images**:
   * Vector graphics are not ideal for highly detailed or photorealistic images, as they rely on geometric shapes rather than individual pixels. This makes them unsuitable for representing textures, gradients, or intricate photographic details[^7][^8][^9].
2. **Time-Consuming Creation**:
   * Designing vector images can be more time-intensive, especially for complex illustrations, requiring precision and expertise with specialized software[^7][^8].
3. **Skill and Software Requirements**:
   * Working with vectors often demands proficiency in vector-based design tools like Adobe Illustrator. Additionally, raster-based programs cannot effectively handle vector files, leading to compatibility issues[^8][^10].
4. **Conversion Challenges**:
   * While converting vectors to rasters is straightforward, the reverse process (raster to vector) is computationally complex and less accurate[^8][^10].
5. **Compatibility Limitations**:
   * Some devices, web browsers, or software may not fully support vector formats, requiring rasterization for proper display or functionality[^7][^11].
6. **Performance Issues in Web Applications**:
   * Vector files like SVGs can introduce performance challenges in web environments due to their handling as HTML-like elements rather than traditional image files[^11].

# Ok I think I’m convinced why I should rasterize, but what is rasterization really?

Put simply, rasterization is the process of turning the shapes or objects (like triangles or polygons) from an image into a grid of tiny squares called pixels. Imagine drawing a triangle on graph paper: rasterization figures out which squares the triangle covers and fills them with color. This makes it possible to display the triangle as an image on a screen or use it in analysis.

A good video visualizing this is here: [DATA CONVERSION IN GIS- RASTERIZATION & VECTORIZATION](https://www.youtube.com/watch?v=8aU031e1KiY)

# I see, so what are the common strategies to actually _do_ rasterization?

### Common Rasterization Strategies in Environmental Health Data Science

1. **Grid-Based Rasterization**:
   * Divides the study area into a uniform grid of cells, assigning values based on the presence or attributes of vector features within each cell. This is widely used for environmental exposure modeling and landscape metrics[^13][^15].
2. **Proximity-Based Rasterization**:
   * Converts vector data into rasters by calculating distances from features (e.g., pollution sources, rivers) and assigning values based on proximity. This is useful for exposure assessments and susceptibility mapping[^20].
3. **Weighted Aggregation**:
   * Rasterizes vector data by assigning weights to features (e.g., land use types or pollution levels) and aggregating them into raster cells. This supports risk stratification and environmental health evaluations[^18][^19].
4. **Interpolation-Based Rasterization**:
   * Uses interpolation methods to convert sparse vector points (e.g., monitoring stations) into continuous raster surfaces, such as air quality or temperature maps[^15][^20].
5. **Multi-Layer Integration**:
   * Combines multiple rasterized datasets (e.g., climate, socioeconomic, and pollution layers) to model complex environmental health interactions[^15][^17].

These strategies enable efficient spatial analysis and integration of diverse environmental health datasets.

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
# Footnotes
- [^1]: [Raster vs Vector: Which is Best for Your GIS Needs? - RisingWave](https://risingwave.com/blog/raster-vs-vector-which-is-best-for-your-gis-needs/)
- [^2]: [Raster vs. Vector Data in GIS | Ankur S. | LinkedIn](https://www.linkedin.com/posts/ankur-s-803497126_gis-geospatial-datascience-activity-7211352628662853634-UrJ4)
- [^3]: [Introduction to image and raster data—ArcGIS Pro](https://pro.arcgis.com/en/pro-app/latest/help/data/imagery/introduction-to-raster-data.htm)
- [^4]: [Data Science in Environmental Health Research - PubMed Central](https://pmc.ncbi.nlm.nih.gov/articles/PMC6853613/)
- [^5]: [Data Science in Environmental Health Research - PubMed](https://pubmed.ncbi.nlm.nih.gov/31723546/)
- [^6]: [Uncovering the Value of Raster Data Applications - Foursquare](https://location.foursquare.com/resources/blog/use-cases/uncovering-the-value-of-raster-data-applications-for-environmental-industries/)
- [^7]: [Advantages And Disadvantages Of Vector Images - Zdigitizing](https://zdigitizing.com/advantages-and-disadvantages-of-vector-images/)
- [^8]: [Vector files: How to create, edit and open them | Adobe](https://www.adobe.com/creativecloud/file-types/image/vector.html)
- [^9]: [What are the disadvantages of vector images? - Answers](https://www.answers.com/physics/What_are_the_disadvantages_of_vector_images)
- [^10]: [Vector vs Raster Graphics – Kevuru Games](https://kevurugames.com/blog/vector-vs-raster-graphics-the-complete-guide-to-pros-cons-and-applications/)
- [^11]: [Are there downsides to using vector images? - Reddit](https://www.reddit.com/r/Wordpress/comments/1116bll/are_there_downsides_to_using_vector_images/)
- [^13]: [A Comparison of Vector and Raster GIS Methods - USDA](https://research.fs.usda.gov/treesearch/6065)
- [^15]: [A review of geospatial exposure models - Nature](https://www.nature.com/articles/s41370-024-00712-8)
- [^17]: [Children's Environmental Health Indicators - Vital Strategies](https://www.vitalstrategies.org/childrens-environmental-health-indicators/)
- [^18]: [Optimization strategy for community planning - Frontiers](https://www.frontiersin.org/journals/public-health/articles/10.3389/fpubh.2024.1347122/full)
- [^19]: [Statistical Methods for Linking Health, Exposure, and Hazards - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC1247575/)
- [^20]: [Using Geographic Information Systems for Exposure Assessment - EHP](https://ehp.niehs.nih.gov/doi/full/10.1289/ehp.6738)
