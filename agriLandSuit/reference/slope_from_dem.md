# Slope from a digital elevation model

Slope from a digital elevation model

## Usage

``` r
slope_from_dem(dem, unit = c("degrees", "percent"), template = NULL)
```

## Arguments

- dem:

  Single-layer elevation \`SpatRaster\` (m).

- unit:

  \`degrees\` or \`percent\`.

- template:

  Optional grid to which slope is averaged after computation at the
  native resolution (cell-mean slope, as used for coarse grids).

## Value

Single-layer \`SpatRaster\` named \`slope_deg\` or \`slope_pct\`.

## Examples

``` r
dem <- terra::rast(ncols = 30, nrows = 30, xmin = 30, xmax = 30.25, ymin = -20, ymax = -19.75, crs = "EPSG:4326")
terra::values(dem) <- as.vector(outer(1:30, 1:30, function(i, j) 500 + 8 * i + 3 * j))
s <- slope_from_dem(dem)
terra::global(s, "mean", na.rm = TRUE)
#>               mean
#> slope_deg 0.557327
slope_from_dem(dem, template = terra::aggregate(dem, 10))
#> class       : SpatRaster
#> size        : 3, 3, 1  (nrow, ncol, nlyr)
#> resolution  : 0.08333333, 0.08333333  (x, y)
#> extent      : 30, 30.25, -20, -19.75  (xmin, xmax, ymin, ymax)
#> coord. ref. : lon/lat WGS 84 (EPSG:4326)
#> source(s)   : memory
#> name        : slope_deg
#> min value   :  0.557082
#> max value   :  0.557573
```
