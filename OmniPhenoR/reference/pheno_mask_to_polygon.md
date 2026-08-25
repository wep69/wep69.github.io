# Convert a binary mask to polygons using terra

Convert a binary mask to polygons using terra

## Usage

``` r
pheno_mask_to_polygon(mask, crs = "")
```

## Arguments

- mask:

  Binary matrix.

- crs:

  Optional CRS string.

## Value

`sf` polygon object.

## Examples

``` r
if (requireNamespace("terra",quietly=TRUE) &&
    requireNamespace("sf",quietly=TRUE)) pheno_mask_to_polygon(pheno_data("leaf_mask"))
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 18 ymin: 28 xmax: 110 ymax: 68
#> CRS:           NA
#>   lyr.1                       geometry
#> 1     1 POLYGON ((54 68, 54 67, 47 ...
if (requireNamespace("terra",quietly=TRUE) &&
    requireNamespace("sf",quietly=TRUE)) pheno_mask_to_polygon(matrix(c(0,1,1,0),2,2))
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: MULTIPOLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 0 ymin: 0 xmax: 2 ymax: 2
#> CRS:           NA
#>   lyr.1                       geometry
#> 1     1 MULTIPOLYGON (((0 2, 0 1, 1...
if (requireNamespace("terra",quietly=TRUE) &&
    requireNamespace("sf",quietly=TRUE)) pheno_mask_to_polygon(pheno_data("leaf_mask"),
    crs = "EPSG:3857")
#> Simple feature collection with 1 feature and 1 field
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 18 ymin: 28 xmax: 110 ymax: 68
#> Projected CRS: WGS 84 / Pseudo-Mercator
#>   lyr.1                       geometry
#> 1     1 POLYGON ((54 68, 54 67, 47 ...
```
