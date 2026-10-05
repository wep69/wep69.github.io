# Evaluate land suitability across climate scenarios

Evaluate land suitability across climate scenarios

## Usage

``` r
scenario_suitability(
  x,
  crop,
  method = c("limiting", "weighted_arithmetic", "weighted_geometric"),
  weights = NULL,
  constraints = NULL,
  engine = c("r", "python", "auto"),
  strict = TRUE,
  strict_units = TRUE,
  na_policy = c("propagate", "available"),
  epsilon = 1e-12,
  breaks = c(0, 0.25, 0.5, 0.75, 1),
  labels = c("N", "S3", "S2", "S1")
)
```

## Arguments

- x:

  An \`agri_scenario_set\`.

- crop:

  An \`agri_crop_profile\`.

- method:

  Aggregation method passed to \`land_suitability()\`.

- weights:

  Optional criterion weights shared across scenarios.

- constraints:

  NULL, one \`agri_constraint_effects\` object shared across scenarios,
  or a named list keyed by scenario ID.

- engine:

  Criterion-scoring engine: \`r\`, \`python\`, or \`auto\`.

- strict:

  Require every crop requirement to have a matching land layer.

- strict_units:

  Require source-layer units to match crop requirements.

- na_policy:

  Missing-value policy passed to \`land_suitability()\`.

- epsilon:

  Numerical floor passed to geometric aggregation.

- breaks, labels:

  Suitability classification specification.

## Value

An \`agri_scenario_suitability\` object.

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
ss
#> <agri_scenario_suitability> 2 scenarios; baseline: baseline ; method: limiting 
terra::values(ss$results$drier$score)
#>       suitability
#>  [1,]   0.0000000
#>  [2,]   0.0000000
#>  [3,]   0.0000000
#>  [4,]   0.0000000
#>  [5,]   0.0000000
#>  [6,]   0.0000000
#>  [7,]   0.0000000
#>  [8,]   0.0000000
#>  [9,]   0.5833333
#> [10,]   0.5000000
#> [11,]   0.4000000
#> [12,]   0.0000000
```
