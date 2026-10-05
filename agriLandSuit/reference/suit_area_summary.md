# Area by suitability class and zone

Classifies a suitability raster and sums cell areas (computed on the
ellipsoid with \`terra::cellSize()\`) by class and, optionally, by zone
(province, district, agro-ecological zone). Cells flagged in \`exclude\`
are reported as a separate class.

## Usage

``` r
suit_area_summary(
  x,
  zones = NULL,
  zone_field = NULL,
  exclude = NULL,
  breaks = c(0, 0.25, 0.5, 0.75, 1),
  labels = c("N", "S3", "S2", "S1"),
  closed = "left",
  unit = c("ha", "km2", "kha", "Mha"),
  excluded_label = "Excluded"
)
```

## Arguments

- x:

  Single-layer \`SpatRaster\` of scores, or an \`agri_suitability\` with
  a raster score.

- zones:

  Optional \`SpatRaster\` of zone codes, or \`SpatVector\` of polygons
  (rasterised by polygon order).

- zone_field:

  Attribute of \`zones\` (SpatVector) used as zone label.

- exclude:

  Optional \`SpatRaster\`; cells equal to 1 are counted as
  \`excluded_label\`.

- breaks, labels, closed:

  Class definition (\`closed = "left"\` by default).

- unit:

  Area unit: \`ha\`, \`km2\`, \`kha\` or \`Mha\`.

- excluded_label:

  Label of excluded cells.

## Value

Data frame with \`zone\`, \`class\`, \`area\` and \`share_pct\` (within
zone).

## Examples

``` r
r <- terra::rast(ncols = 20, nrows = 10, xmin = 30, xmax = 32, ymin = -20, ymax = -19, crs = "EPSG:4326")
set.seed(6); terra::values(r) <- runif(terra::ncell(r))
zones <- r; terra::values(zones) <- rep(1:2, each = 100)
excl <- r * 0; excl[1:20] <- 1
suit_area_summary(r, zones = zones, exclude = excl, unit = "km2")
#>   zone    class     area share_pct
#> 1    1        N 2210.483  18.99406
#> 2    1       S3 2559.338  21.99168
#> 3    1       S2 1861.003  15.99108
#> 4    1       S1 2676.619  22.99944
#> 5    1 Excluded 2330.314  20.02374
#> 6    2        N 3480.596  29.99815
#> 7    2       S3 2668.907  23.00247
#> 8    2       S2 2205.080  19.00489
#> 9    2       S1 3248.117  27.99449
```
