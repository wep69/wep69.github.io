# Validate a land data container

Validate a land data container

## Usage

``` r
land_validate(x, strict = TRUE, require_units = FALSE)
```

## Arguments

- x:

  An \`agri_land_data\` object.

- strict:

  If TRUE, geometry mismatches are errors; otherwise warnings.

- require_units:

  Require units for every environmental layer.

## Value

An \`agri_validation\` object.

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
land_validate(land, strict = TRUE, require_units = TRUE)
#> <agri_validation> OK
#> No issues detected.
```
