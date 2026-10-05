# Summarize area or cell counts for suitability transitions

Summarize area or cell counts for suitability transitions

## Usage

``` r
scenario_area_change(
  x,
  baseline = NULL,
  scenario = NULL,
  unit = c("ha", "km2", "m2", "cells")
)
```

## Arguments

- x:

  An \`agri_scenario_transition\` object or an
  \`agri_scenario_suitability\` object.

- baseline, scenario:

  Passed to \`scenario_transition()\` when needed.

- unit:

  One of \`cells\`, \`ha\`, \`km2\`, or \`m2\`.

## Value

A data.frame of class transitions with class change and area/count,
inheriting from \`agri_scenario_area\`.

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
scenario_area_change(ss, unit = "km2")
#> <agri_scenario_area> baseline: baseline 
#>  scenario_id transition_code baseline_class future_class change    amount unit
#>        drier               1              N            N      0 105390.29  km2
#>        drier               6             S3           S3      0  23240.85  km2
#>        drier              11             S2           S2      0  11620.42  km2
```
