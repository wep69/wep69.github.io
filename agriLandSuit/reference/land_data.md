# Create an agricultural land data container

Create an agricultural land data container

## Usage

``` r
land_data(
  climate = NULL,
  soil = NULL,
  terrain = NULL,
  water = NULL,
  constraints = NULL,
  template = NULL,
  units = NULL,
  metadata = list(),
  validate = TRUE
)
```

## Arguments

- climate, soil, terrain, water, constraints:

  A \`terra::SpatRaster\`, raster file path, named list of
  rasters/paths, or NULL.

- template:

  Optional SpatRaster used as the target geometry. If NULL, the first
  available layer is used.

- units:

  Named character vector keyed by bare layer name or \`domain.layer\`.

- metadata:

  Named list of provenance or study metadata.

- validate:

  Logical; validate geometry and metadata immediately.

## Value

An object of class \`agri_land_data\`.

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
land
#> <agri_land_data>
#>  domains : climate, soil, terrain, water 
#>  layers  : 4 
#>  geometry: 3 x 4 cells; resolution 1 x 1 
#>  CRS     : +proj=longlat +datum=WGS84 +no_defs 
land_validate(land, require_units = TRUE)$ok
#> [1] TRUE
```
