# Convert detection boxes to simple features

Convert detection boxes to simple features

## Usage

``` r
pheno_detection_to_sf(x, crs = NULL)
```

## Arguments

- x:

  `pheno_detection`.

- crs:

  Optional CRS.

## Value

`sf` object when sf is installed.

## Examples

``` r
d<-pheno_detection(data.frame(class="leaf",confidence=.9,xmin=1,ymin=2,xmax=5,ymax=7))
if (requireNamespace("sf",quietly=TRUE)) pheno_detection_to_sf(d)
#> Simple feature collection with 1 feature and 12 fields
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1 ymin: 2 xmax: 5 ymax: 7
#> CRS:           NA
#>   class confidence xmin ymin xmax ymax object_id centroid_x centroid_y
#> 1  leaf        0.9    1    2    5    7         1          3        4.5
#>   bbox_width bbox_height bbox_area                       geometry
#> 1          4           5        20 POLYGON ((1 2, 5 2, 5 7, 1 ...
if (requireNamespace("sf",quietly=TRUE)) { dn <- pheno_coordinates(d, 10, 10, "normalized_xy"); 
    pheno_detection_to_sf(pheno_coordinates(dn, 10, 10, "pixel_xy")) }
#> Simple feature collection with 1 feature and 12 fields
#> Geometry type: POLYGON
#> Dimension:     XY
#> Bounding box:  xmin: 1 ymin: 2 xmax: 5 ymax: 7
#> CRS:           NA
#>   class confidence xmin ymin xmax ymax object_id centroid_x centroid_y
#> 1  leaf        0.9    1    2    5    7         1          3        4.5
#>   bbox_width bbox_height bbox_area                       geometry
#> 1          4           5        20 POLYGON ((1 2, 5 2, 5 7, 1 ...
```
