# Extract environmental layers

Extract environmental layers

## Usage

``` r
land_layers(x, domain = NULL)
```

## Arguments

- x:

  An \`agri_land_data\` object.

- domain:

  Optional domain name.

## Value

A named list of SpatRaster objects or one SpatRaster.

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
names(land_layers(land, "terrain"))
#> [1] "slope"
land_layers(land)
#> $climate
#> class       : SpatRaster
#> size        : 3, 4, 1  (nrow, ncol, nlyr)
#> resolution  : 1, 1  (x, y)
#> extent      : 30, 34, -20, -17  (xmin, xmax, ymin, ymax)
#> coord. ref. : lon/lat WGS 84 (EPSG:4326)
#> source(s)   : memory
#> name        : Pseason
#> min value   :     400
#> max value   :    1500
#> 
#> $soil
#> class       : SpatRaster
#> size        : 3, 4, 1  (nrow, ncol, nlyr)
#> resolution  : 1, 1  (x, y)
#> extent      : 30, 34, -20, -17  (xmin, xmax, ymin, ymax)
#> coord. ref. : lon/lat WGS 84 (EPSG:4326)
#> source(s)   : memory
#> name        :  pH
#> min value   :   5
#> max value   : 7.8
#> 
#> $terrain
#> class       : SpatRaster
#> size        : 3, 4, 1  (nrow, ncol, nlyr)
#> resolution  : 1, 1  (x, y)
#> extent      : 30, 34, -20, -17  (xmin, xmax, ymin, ymax)
#> coord. ref. : lon/lat WGS 84 (EPSG:4326)
#> source(s)   : memory
#> name        : slope
#> min value   :     2
#> max value   :    25
#> 
#> $water
#> class       : SpatRaster
#> size        : 3, 4, 1  (nrow, ncol, nlyr)
#> resolution  : 1, 1  (x, y)
#> extent      : 30, 34, -20, -17  (xmin, xmax, ymin, ymax)
#> coord. ref. : lon/lat WGS 84 (EPSG:4326)
#> source(s)   : memory
#> name        : LGP
#> min value   :  60
#> max value   : 140
#> 
```
