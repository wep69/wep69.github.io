# Score a crop requirement against its matching land layer

Score a crop requirement against its matching land layer

## Usage

``` r
land_criterion(
  land,
  requirement,
  engine = c("r", "python", "auto"),
  strict_units = TRUE
)
```

## Arguments

- land:

  An \`agri_land_data\` object.

- requirement:

  An \`agri_crop_requirement\`.

- engine:

  \`"r"\`, \`"python"\`, or \`"auto"\`.

- strict_units:

  If TRUE, missing or non-identical unit metadata cause an error.

## Value

An \`agri_criterion_suit\` object.

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
req <- crop_requirement("slope", "terrain", "degree", "decreasing", c(5, 20), source = "illustrative", source_id = "demo-slope")
lc <- land_criterion(land, req)
terra::values(lc$score)
#>           slope
#>  [1,] 1.0000000
#>  [2,] 0.9333333
#>  [3,] 0.4000000
#>  [4,] 0.0000000
#>  [5,] 1.0000000
#>  [6,] 0.9333333
#>  [7,] 0.4000000
#>  [8,] 0.0000000
#>  [9,] 1.0000000
#> [10,] 0.9333333
#> [11,] 0.4000000
#> [12,] 0.0000000
```
