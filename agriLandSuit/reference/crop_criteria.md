# Score all available requirements in a crop profile

Score all available requirements in a crop profile

## Usage

``` r
crop_criteria(
  land,
  crop,
  engine = c("r", "python", "auto"),
  strict = TRUE,
  strict_units = TRUE
)
```

## Arguments

- land:

  An \`agri_land_data\` object.

- crop:

  An \`agri_crop_profile\`.

- engine:

  \`"r"\`, \`"python"\`, or \`"auto"\`.

- strict:

  If TRUE, every crop requirement must have a matching land layer.

- strict_units:

  If TRUE, unit metadata must match exactly.

## Value

An \`agri_criterion_set\` object. No cross-criterion aggregation is
performed by this function. Use \`suit_aggregate()\` or
\`land_suitability()\` for composition.

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
crit <- crop_criteria(land, crop)
crit
#> <agri_criterion_set> demo_maize 
#>  scored : 4 criterion/criteria
#>  missing: none 
criterion_scores(crit)
#> class       : SpatRaster
#> size        : 3, 4, 4  (nrow, ncol, nlyr)
#> resolution  : 1, 1  (x, y)
#> extent      : 30, 34, -20, -17  (xmin, xmax, ymin, ymax)
#> coord. ref. : lon/lat WGS 84 (EPSG:4326)
#> source(s)   : memory
#> names       : Pseason,      LGP,  pH, slope
#> min values  :       0,        0, 0.5,     0
#> max values  :       1, 0.833333,   1,     1
```
