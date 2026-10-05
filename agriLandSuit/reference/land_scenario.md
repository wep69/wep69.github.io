# Create a land scenario

A scenario binds one validated \`agri_land_data\` object to explicit
scenario metadata. Scenario metadata are kept separate from crop
requirements and decision weights so the same crop model can be
evaluated consistently across baseline and future conditions.

## Usage

``` r
land_scenario(
  land,
  scenario_id,
  model = "unspecified",
  period = "unspecified",
  management = "unspecified",
  pathway = NULL,
  baseline = FALSE,
  metadata = list()
)
```

## Arguments

- land:

  An \`agri_land_data\` object.

- scenario_id:

  Unique scenario identifier.

- model:

  Climate model, observational product, or scenario data source.

- period:

  Period label, for example \`"1991-2020"\` or \`"2041-2070"\`.

- management:

  Management label such as \`"rainfed"\` or \`"irrigated"\`.

- pathway:

  Optional forcing/pathway label such as \`"SSP2-4.5"\`.

- baseline:

  Logical; whether this is the reference scenario.

- metadata:

  Additional named metadata.

## Value

An object of class \`agri_land_scenario\`.

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
land_scenario(fut, "drier", model = "synthetic", period = "2041-2060", management = "rainfed", pathway = "SSP2-4.5")
#> <agri_land_scenario> drier 
#>  model     : synthetic 
#>  period    : 2041-2060 
#>  management: rainfed 
#>  pathway   : SSP2-4.5 
#>  baseline  : FALSE 
```
