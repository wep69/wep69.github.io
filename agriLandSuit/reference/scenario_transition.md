# Build class-transition maps or vectors

Build class-transition maps or vectors

## Usage

``` r
scenario_transition(x, baseline = NULL, scenario = NULL)
```

## Arguments

- x:

  An \`agri_scenario_suitability\` object.

- baseline:

  Optional baseline scenario ID.

- scenario:

  Optional comparison scenario IDs.

## Value

An \`agri_scenario_transition\` object with one transition object per
comparison scenario and a stable transition key.

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
tr <- scenario_transition(ss)
head(tr$key)
#>   transition_code baseline_code future_code baseline_class future_class change
#> 1               1             1           1              N            N      0
#> 2               2             1           2              N           S3      1
#> 3               3             1           3              N           S2      2
#> 4               4             1           4              N           S1      3
#> 5               5             2           1             S3            N     -1
#> 6               6             2           2             S3           S3      0
```
