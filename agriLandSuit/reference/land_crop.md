# Crop all land layers consistently

Crop all land layers consistently

## Usage

``` r
land_crop(x, y, mask = FALSE)
```

## Arguments

- x:

  An \`agri_land_data\` object.

- y:

  A SpatVector, SpatRaster, SpatExtent, or object accepted by
  \`terra::crop\`.

- mask:

  Logical; mask to \`y\` after cropping when supported.

## Value

Cropped \`agri_land_data\`.

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
small <- land_crop(land, terra::ext(30, 32, -20, -18.5))
terra::ncell(small$template)
#> [1] 2
```
