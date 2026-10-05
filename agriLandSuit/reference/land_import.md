# Import an object exported by \`land_export()\`

Import an object exported by \`land_export()\`

## Usage

``` r
land_import(path, validate = TRUE)
```

## Arguments

- path:

  Bundle directory or packed \`.rds\` file.

- validate:

  If TRUE, run class-specific validation where available.

## Value

Reconstructed R object.

## Examples

``` r
r <- terra::rast(ncols = 4, nrows = 3, xmin = 30, xmax = 34, ymin = -20, ymax = -17, crs = "EPSG:4326")
lay <- function(v, n) { x <- r; terra::values(x) <- v; names(x) <- n; x }
x <- land_data(climate = lay(1:12, "Pseason"), units = c(climate.Pseason = "mm"))
d <- tempfile("agri_bundle_")
land_export(x, d, format = "bundle", manifest = requireNamespace("jsonlite", quietly = TRUE))
land_import(d)
#> <agri_land_data>
#>  domains : climate 
#>  layers  : 1 
#>  geometry: 3 x 4 cells; resolution 1 x 1 
#>  CRS     : +proj=longlat +datum=WGS84 +no_defs 
```
