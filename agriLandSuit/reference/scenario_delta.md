# Compute suitability change relative to a baseline

Compute suitability change relative to a baseline

## Usage

``` r
scenario_delta(
  x,
  baseline = NULL,
  scenario = NULL,
  type = c("absolute", "relative"),
  epsilon = 1e-12
)
```

## Arguments

- x:

  An \`agri_scenario_suitability\` object.

- baseline:

  Optional baseline scenario ID; defaults to the set baseline.

- scenario:

  Optional comparison scenario IDs; defaults to all non-baseline
  scenarios.

- type:

  \`absolute\` for future minus baseline, or \`relative\` for the
  difference divided by the absolute baseline score.

- epsilon:

  Baseline magnitudes at or below epsilon become NA for relative change.

## Value

An \`agri_scenario_delta\` object.

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
fut <- land_data(
  climate = lay(seq(400, 1500, length.out = 12) * 0.8, "Pseason"),
  water = lay(rep(c(45, 85, 125), each = 4), "LGP"),
  soil = lay(rep(c(5.0, 6.2, 7.8), 4), "pH"),
  terrain = lay(rep(c(2, 6, 14, 25), 3), "slope"),
  units = c(climate.Pseason = "mm", water.LGP = "day", soil.pH = "pH", terrain.slope = "degree"))
sset <- scenario_set(
  land_scenario(land, "baseline", model = "observed", period = "1991-2020", management = "rainfed", baseline = TRUE),
  land_scenario(fut, "drier", model = "synthetic", period = "2041-2060", management = "rainfed", pathway = "SSP2-4.5"))
ss <- scenario_suitability(sset, crop)
d <- scenario_delta(ss)
terra::values(d$delta$drier)
#>            delta
#>  [1,]  0.0000000
#>  [2,]  0.0000000
#>  [3,]  0.0000000
#>  [4,]  0.0000000
#>  [5,] -0.1666667
#>  [6,] -0.1666667
#>  [7,] -0.1666667
#>  [8,]  0.0000000
#>  [9,] -0.1166667
#> [10,]  0.0000000
#> [11,]  0.0000000
#> [12,]  0.0000000
```
