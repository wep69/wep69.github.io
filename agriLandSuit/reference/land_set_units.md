# Set unit metadata

Set unit metadata

## Usage

``` r
land_set_units(x, units, validate = TRUE)
```

## Arguments

- x:

  An \`agri_land_data\` object.

- units:

  Named character vector.

- validate:

  Logical.

## Value

Updated \`agri_land_data\`.

## Examples

``` r
r <- terra::rast(ncols = 4, nrows = 3, xmin = 30, xmax = 34, ymin = -20, ymax = -17, crs = "EPSG:4326")
lay <- function(v, n) { x <- r; terra::values(x) <- v; names(x) <- n; x }
land <- land_data(climate = lay(seq(400, 1500, length.out = 12), "Pseason"), validate = FALSE)
land <- land_set_units(land, c(climate.Pseason = "mm"))
land_units(land)
#> climate.Pseason 
#>            "mm" 
```
