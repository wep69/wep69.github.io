# Inspect or validate an external interoperability contract

Inspect or validate an external interoperability contract

## Usage

``` r
external_contract(
  type = c("raster", "land_data", "climate_risk", "suitability"),
  x = NULL
)
```

## Arguments

- type:

  One of \`raster\`, \`land_data\`, \`climate_risk\`, or
  \`suitability\`.

- x:

  Optional object to validate against the selected contract.

## Value

An \`agri_external_contract\` when \`x\` is NULL, otherwise an
\`agri_validation\` object.

## Examples

``` r
r <- terra::rast(ncols = 4, nrows = 3, xmin = 30, xmax = 34, ymin = -20, ymax = -17, crs = "EPSG:4326")
lay <- function(v, n) { x <- r; terra::values(x) <- v; names(x) <- n; x }
x <- land_data(climate = lay(1:12, "Pseason"), units = c(climate.Pseason = "mm"))
external_contract("land_data")
#> <agri_external_contract> land_data 
#>  expected class: agri_land_data 
external_contract("land_data", x)$ok
#> [1] TRUE
```
