# Create a validated set of land scenarios

Create a validated set of land scenarios

## Usage

``` r
scenario_set(..., require_baseline = TRUE, strict_geometry = TRUE)
```

## Arguments

- ...:

  Two or more \`agri_land_scenario\` objects, or one list containing
  them.

- require_baseline:

  Require exactly one baseline scenario.

- strict_geometry:

  Require all scenario templates to share CRS, extent, rows, columns,
  resolution, and origin.

## Value

An \`agri_scenario_set\`.

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
sset
#> <agri_scenario_set> 2 scenarios; baseline: baseline 
summary(sset)
#>   scenario_id     model    period management  pathway baseline
#> 1    baseline  observed 1991-2020    rainfed     <NA>     TRUE
#> 2       drier synthetic 2041-2060    rainfed SSP2-4.5    FALSE
```
