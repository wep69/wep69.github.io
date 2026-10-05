# Combine evaluated rules into exclusion, cap, and penalty effects

Multiple caps use the most restrictive (minimum) cap. Multiple penalties
are multiplicative. Any active exclusion marks the cell as excluded.

## Usage

``` r
constraint_effects(x)
```

## Arguments

- x:

  An \`agri_constraint_eval\` object.

## Value

An \`agri_constraint_effects\` object with three aligned SpatRasters:
\`excluded\`, \`cap\`, and \`penalty\`.

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
terra::values(eff$excluded)
#>       excluded
#>  [1,]        0
#>  [2,]        0
#>  [3,]        0
#>  [4,]        1
#>  [5,]        0
#>  [6,]        0
#>  [7,]        0
#>  [8,]        1
#>  [9,]        0
#> [10,]        0
#> [11,]        0
#> [12,]        1
terra::values(eff$cap)
#>       cap
#>  [1,] 0.5
#>  [2,] 1.0
#>  [3,] 1.0
#>  [4,] 0.5
#>  [5,] 1.0
#>  [6,] 1.0
#>  [7,] 0.5
#>  [8,] 1.0
#>  [9,] 1.0
#> [10,] 0.5
#> [11,] 1.0
#> [12,] 1.0
```
