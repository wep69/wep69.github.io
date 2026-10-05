# Apply explicit land constraints to one suitability score

The operation order is cap, then multiplicative penalty, then exclusion.
Unknown constraint states propagate as \`NA\`.

## Usage

``` r
apply_constraints(x, effects, excluded_value = NA_real_)
```

## Arguments

- x:

  A single-layer \`terra::SpatRaster\` suitability score or an
  \`agri_criterion_suit\` object.

- effects:

  An \`agri_constraint_effects\` object.

- excluded_value:

  Value assigned to excluded cells. The default \`NA\` preserves
  exclusion as distinct from agronomic unsuitability.

## Value

A constrained SpatRaster or updated \`agri_criterion_suit\` object.

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
eff <- constraint_effects(constraint_evaluate(land, rules))
score <- lay(rep(0.9, 12), "score")
terra::values(apply_constraints(score, eff))
#>       score
#>  [1,]   0.5
#>  [2,]   0.9
#>  [3,]   0.9
#>  [4,]    NA
#>  [5,]   0.9
#>  [6,]   0.9
#>  [7,]   0.5
#>  [8,]    NA
#>  [9,]   0.9
#> [10,]   0.5
#> [11,]   0.9
#> [12,]    NA
terra::values(apply_constraints(score, eff, excluded_value = 0))
#>       score
#>  [1,]   0.5
#>  [2,]   0.9
#>  [3,]   0.9
#>  [4,]   0.0
#>  [5,]   0.9
#>  [6,]   0.9
#>  [7,]   0.5
#>  [8,]   0.0
#>  [9,]   0.9
#> [10,]   0.5
#> [11,]   0.9
#> [12,]   0.0
```
