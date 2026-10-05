# Extract criterion suitability scores

Extract criterion suitability scores

## Usage

``` r
criterion_scores(x, stack = TRUE)
```

## Arguments

- x:

  An \`agri_criterion_set\`.

- stack:

  If TRUE and every score is a SpatRaster with matching geometry, return
  one multi-layer SpatRaster; otherwise return a named list.

## Value

A SpatRaster or named list.

## Examples

``` r
r <- terra::rast(ncols = 4, nrows = 3, xmin = 30, xmax = 34, ymin = -20, ymax = -17, crs = "EPSG:4326")
lay <- function(v, n) { x <- r; terra::values(x) <- v; names(x) <- n; x }
land <- land_data(
  climate = lay(seq(400, 1500, length.out = 12), "Pseason"),
  water = lay(rep(c(60, 100, 140), each = 4), "LGP"),
  soil = lay(rep(c(5.0, 6.2, 7.8), 4), "pH"),
  terrain = lay(rep(c(2, 6, 14, 25), 3), "slope"),
  units = c(climate.Pseason = "mm", water.LGP = "day", soil.pH = "pH", terrain.slope = "degree"))
crop <- crop_profile("demo_maize", "Zea mays", common_name = "maize", requirements = list(
  crop_requirement("Pseason", "climate", "mm", "range", c(400, 600, 1200, 1800), source = "illustrative", source_id = "demo-rain"),
  crop_requirement("LGP", "water", "day", "increasing", c(90, 150), source = "illustrative", source_id = "demo-lgp"),
  crop_requirement("pH", "soil", "pH", "range", c(4.5, 5.5, 7.5, 8.5), source = "illustrative", source_id = "demo-ph"),
  crop_requirement("slope", "terrain", "degree", "decreasing", c(5, 20), source = "illustrative", source_id = "demo-slope")))
crit <- crop_criteria(land, crop)
sc <- criterion_scores(crit)
names(sc)
#> [1] "Pseason" "LGP"     "pH"      "slope"  
criterion_scores(crit, stack = FALSE)[[1]]
#> class       : SpatRaster
#> size        : 3, 4, 1  (nrow, ncol, nlyr)
#> resolution  : 1, 1  (x, y)
#> extent      : 30, 34, -20, -17  (xmin, xmax, ymin, ymax)
#> coord. ref. : lon/lat WGS 84 (EPSG:4326)
#> source(s)   : memory
#> name        : Pseason
#> min value   :       0
#> max value   :       1
```
