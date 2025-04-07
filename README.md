![image](https://github.com/user-attachments/assets/32d68b98-6b60-4633-8bcf-bb255dd628b0)


# spatial_aggregations_tutorial
Walk through on all-things spatial aggregations. The main focus is aggregating climate/exposure variables over (raster) grids into (GIS vector) polygons. 

____

This tutorial focuses on:

1. **Frequently asked questions**

* What is the difference between gis vector data and rasters?
* What is the difference between overlay vs rasterize?
* What are multiple rasterization strategies?
* What is the difference between downscaling and upsampling?
* What are computationally efficient options for spatial aggregations?
* How can I perform discrete variable aggregations?

2. **Example scripts and end-to-end pipelines for spatial aggregations**

* Python example script for spatial aggregations using rasterization (`rasterstats` package)
* Python example script for discrete variable aggregations: climate types on the USA
* Blazing fast end-to-end pipelines for spatial aggregations


_____

### Main Applications of Spatial Aggregations in Environmental Health Data Science

1. **Environmental Monitoring and Modeling**:
   * Spatial aggregations enables the integration of satellite imagery with vector data, such as habitat boundaries, to monitor ecosystem changes, deforestation, and wildfire spread[^1][^2].
   * It supports modeling continuous environmental phenomena like air pollution, temperature, and precipitation across landscapes[^1][^3].
2. **Exposure Assessment, Urban Planning and Public Health**
   * Spatial aggregations of climate and pollution data are used to estimate individual or population-level exposure to environmental hazards (e.g., heat waves or air pollutants) by overlaying grids with demographic polygons[^4][^5].
4. **Disaster Preparedness and Response**:
   * Spatial aggregations are crucial for tracking natural disasters like floods or hurricanes and assessing their health impacts by combining raster hazard maps with population density polygons[^6][^2].
5. **Agricultural and Food Security Studies**:
   * Spatial aggregations of soil temperature, crop health, and irrigation data help assess food security risks and optimize agricultural practices to mitigate environmental health impacts like malnutrition[^6].

- [^1]: [Raster vs Vector: Which is Best for Your GIS Needs? - RisingWave](https://risingwave.com/blog/raster-vs-vector-which-is-best-for-your-gis-needs/)
- [^2]: [Raster vs. Vector Data in GIS | Ankur S. | LinkedIn](https://www.linkedin.com/posts/ankur-s-803497126_gis-geospatial-datascience-activity-7211352628662853634-UrJ4)
- [^3]: [Introduction to image and raster data—ArcGIS Pro](https://pro.arcgis.com/en/pro-app/latest/help/data/imagery/introduction-to-raster-data.htm)
- [^4]: [Data Science in Environmental Health Research - PubMed Central](https://pmc.ncbi.nlm.nih.gov/articles/PMC6853613/)
- [^5]: [Data Science in Environmental Health Research - PubMed](https://pubmed.ncbi.nlm.nih.gov/31723546/)
- [^6]: [Uncovering the Value of Raster Data Applications - Foursquare](https://location.foursquare.com/resources/blog/use-cases/uncovering-the-value-of-raster-data-applications-for-environmental-industries/)

Resources: 
* vector vs raster
  - https://community.alteryx.com/t5/Data-Science/Vector-and-Raster-A-Tale-of-Two-Spatial-Data-Types/ba-p/336141
  - https://www.e-education.psu.edu/geog160/node/1935
  - https://r.geocompx.org/raster-vector
* overlay vs rasterize
  - https://en.wikipedia.org/wiki/Rasterisation
  - https://en.wikipedia.org/wiki/Vector_overlay
  - https://pygis.io/docs/e_raster_rasterize.html
  - https://pygis.io/docs/e_vector_overlay.html
* rasterization
  - https://docs.qgis.org/3.34/en/docs/user_manual/processing_algs/gdal/vectorconversion.html#rasterize-vector-to-raster
  - https://www.ecologi.st/spatial-r/old-raster-gis-operations-in-r-with-raster.html#rasterizing-1
  - vector aggregation vs raster aggregation 
  - https://desktop.arcgis.com/en/arcmap/latest/tools/spatial-analyst-toolbox/how-aggregate-works.htm
* vector overlay vs raster overlay 
  - https://saylordotorg.github.io/text_essentials-of-geographic-information-systems/s12-01-basic-geoprocessing-with-raste.html
  - http://www.gitta.info/Suitability/en/html/unit_BoolOverlay.html
* downscaling
  - https://www.researchgate.net/publication/335651129_An_Overview_of_Theoretical_and_Practical_Issues_in_Spatial_Downscaling_of_Coarse_Resolution_Satellite-derived_Products#pf2
 
Additional GIS resources: https://mapping.share.library.harvard.edu/
