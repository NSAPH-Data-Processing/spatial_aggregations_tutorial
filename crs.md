# What is the Best CRS for Spatial Aggregations through Rasterization?

When doing spatial aggregations — especially through rasterization — validating Coordinate Reference System (CRS) usage is crucial. A CRS can distort areas, angles, or distances, which can seriously mess with your results.

The steps for CRS usage for spatial aggregations are:
1. **Choose the right CRS** The next section will help you understand how to choose the right CRS for your data.
2. **Transform your inputs** Convert both your vector and raster data to the same CRS. This is important because if they are in different CRSs, the rasterization process may not align them correctly.

## Projected vs. Geographic CRS

A **projected CRS** uses "flat world" coordinates (like meters or feet) to represent locations on a flat surface. This is great for area calculations because it keeps distances and areas accurate.

A **geographic CRS** is a representation that uses degree based coordinates to identify locations on the Earth’s surface. The distances in degrees are not uniform. 

> **Area Distortion** Degrees of latitude are roughly equal everywhere on Earth, but degrees of longitude vary in size depending on where you are. Therefore, cells that are 0.1° x 0.1° do not cover the same surface area everywhere on Earth. Near the equator, one longitudinal degree is ~111 km Near the poles, degrees get closer together — longitudinal degrees shrink to almost zero.

To illustrate this, here’s a comparison of the same point in different CRSs, the **Boston City Hall** in Boston, Massachusetts is located at approximately 42.3601° N, 71.0589° W.:

**Boston Coordinates in Different CRSs**
| CRS                              | Type        | X (Longitude / Easting) | Y (Latitude / Northing) |
|----------------------------------|-------------|--------------------------|--------------------------|
| WGS 84 (`EPSG:4326`)             | Geographic  | -71.0589                 | 42.3601                  |
| NAD83 (`EPSG:4269`)              | Geographic  | -71.0589                 | 42.3601                  |
| ETRS89 (`EPSG:4258`)             | Geographic  | -71.0589                 | 42.3601                  |
| Albers Equal Area (`EPSG:5070`)  | Projected   | 2,017,889                | 2,417,509                |
| Web Mercator (`EPSG:3857`)       | Projected   | -7,910,241               | 5,215,074                |


Although WGS 84, NAD83, and ETRS89 all have the same coordinates  expressed in degrees, the exact location they point to may be slightly shifted given the anchoring of the CRS. Depending on the use case, you can choose the CRS that best fits your needs. For example:

* WGS84 — default for GPS, global mapping, web maps (e.g., Leaflet, Google Maps)
* NAD83 — best for US-based work, especially if your other datasets (like census data) are in it
* ETRS89 — best for Europe, especially when you care about stable coordinates over time


## What makes a “good” CRS for rasterization?

The key is to use a projected CRS (not geographic!) that preserves area, so the size of grid cells remains consistent and meaningful across space.

However,some considerations might relax the need to transform to a projected CRS.

**Sphere Region vs. CRS Distortion** if the region of interest is relatively confined to a sphere region away from the poles (mid-latitudes like contiguous U.S.or Western Europe), transforming to a projected CRS is not as important.

**Grid Size vs. CRS Distortion** When using a geographic CRS (like WGS 84), the main issue is that a “square” cell in degrees isn’t truly square in meters — especially as you move away from the equator. But the smaller the cell, the less that distortion matters within the cell.

Small grid cells (e.g., 0.001°)
 * Represent a small patch of Earth — distortion within each cell is minimal
 * Area/shape differences across the cell are negligible
 * More forgiving for spatial aggregations (even in degrees)

Large grid cells (e.g., 1° x 1°)
 * Can span over 100 km, depending on latitude
 * Each cell covers vastly different areas at different latitudes
 * Will introduce noticeable inaccuracies in aggregations if not projected

## Transforming your inputs 

To transform your vector polygon inputs from the source CRS into another CRS, use the `to_crs` method in Geopandas. For example, to transform a polygon from WGS 84 to Web Mercator, you can use the following code:

```python
import geopandas as gpd

# Load the polygon shapefile
polygon = gpd.read_file('path/to/polygon.shp')
# Transform the polygon to Web Mercator
polygon = polygon.to_crs(epsg=3857)
```

To transform your raster data, you can use the `rasterio` library. For example, to transform a raster from WGS 84 to Web Mercator, you can use the following code:

```python
import rasterio
from rasterio import open as rio_open

# Load the raster
with rio_open('path/to/raster.tif') as src:
    # Read the data
    data = src.read()
    # Get the metadata
    meta = src.meta.copy()
    # Transform the raster to Web Mercator
    meta['crs'] = 'EPSG:3857'
    with rio_open('path/to/transformed_raster.tif', 'w', **meta) as dst:
        dst.write(data)
```
## Conclusion
Choosing the right CRS for spatial aggregations through rasterization is crucial for accurate results. By understanding the differences between projected and geographic CRSs, and how to transform your inputs, you can ensure that your spatial analyses are robust and reliable.



 