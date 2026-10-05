# Summarize active constraints

Summarize active constraints

## Usage

``` r
constraint_summary(x)
```

## Arguments

- x:

  An \`agri_constraint_eval\` or \`agri_constraint_effects\` object.

## Value

A data.frame with rule/effect type, active cell count, valid cell count,
and affected fraction.

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
rules <- constraint_set(
  land_constraint("steep", "terrain.slope", "exclude", "gt", threshold = 20, unit = "degree"),
  land_constraint("acid", "soil.pH", "cap", "lt", threshold = 5.5, cap = 0.5, unit = "pH"))
constraint_summary(constraint_evaluate(land, rules))
#>            name    type        source n_active n_valid  fraction
#> n_active  steep exclude terrain.slope        3      12 0.2500000
#> n_active1  acid     cap       soil.pH        4      12 0.3333333
```
