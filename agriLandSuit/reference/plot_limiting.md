# Map of limiting factors

Map of limiting factors

## Usage

``` r
plot_limiting(x, boundary = NULL)
```

## Arguments

- x:

  An \`agri_limiting_map\` with raster index.

- boundary:

  Optional boundary layer.

## Value

A \`ggplot\` object.

## Examples

``` r
r <- terra::rast(ncols = 3, nrows = 2, nlyrs = 3, xmin = 30, xmax = 33, ymin = -20, ymax = -18, crs = "EPSG:4326")
terra::values(r) <- cbind(c(1, .4, .9, 1, .2, .7), c(1, .8, .3, 1, .6, .9), c(1, .9, .8, .5, .7, .1))
names(r) <- c("climate", "soil", "water")
if (requireNamespace("ggplot2", quietly = TRUE)) plot_limiting(limiting_map(r, level = "domain"))
```
