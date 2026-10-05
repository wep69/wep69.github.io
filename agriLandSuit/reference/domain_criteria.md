# Score crop requirements from one environmental domain

This function keeps criterion scores separate. It does not aggregate
them into a composite suitability index.

## Usage

``` r
domain_criteria(
  land,
  crop,
  domain = c("climate", "soil", "terrain", "water"),
  engine = c("r", "python", "auto"),
  strict = TRUE,
  strict_units = TRUE
)
```

## Arguments

- land:

  An \`agri_land_data\` object.

- crop:

  An \`agri_crop_profile\` object.

- domain:

  One of climate, soil, terrain, or water.

- engine:

  \`"r"\`, \`"python"\`, or \`"auto"\`.

- strict:

  If TRUE, every requirement in the selected domain must have a matching
  land layer.

- strict_units:

  If TRUE, source-layer units must match the crop profile.

## Value

An \`agri_domain_criteria\` object, which also inherits from
\`agri_criterion_set\`.

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
domain_criteria(land, crop, domain = "soil")
#> <agri_domain_criteria> demo_maize 
#>  domain : soil 
#>  scored : 1 criterion/criteria
#>  missing: none 
```
