# Evaluate explicit constraints against land layers

Missing source values remain unknown (\`NA\`) rather than being silently
treated as unconstrained.

## Usage

``` r
constraint_evaluate(land, constraints, strict_units = TRUE)
```

## Arguments

- land:

  An \`agri_land_data\` object.

- constraints:

  An \`agri_land_constraint\`, \`agri_constraint_set\`, or list of
  constraint objects.

- strict_units:

  If TRUE, a rule with declared \`unit\` must match source layer unit
  metadata exactly.

## Value

An \`agri_constraint_eval\` object containing one active 0/1 raster per
rule.

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
ev <- constraint_evaluate(land, rules)
ev
#> <agri_constraint_eval> default 
#>  evaluated: 2 rule(s)
```
