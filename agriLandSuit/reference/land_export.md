# Export an agriLandSuit object reproducibly

Export an agriLandSuit object reproducibly

## Usage

``` r
land_export(
  x,
  path,
  format = c("bundle", "rds"),
  overwrite = FALSE,
  manifest = TRUE
)
```

## Arguments

- x:

  Object to export.

- path:

  Destination directory for \`bundle\` or file for \`rds\`.

- format:

  \`bundle\` writes spatial payloads as standard geospatial files plus a
  packed object; \`rds\` embeds spatial objects using \`terra::wrap()\`.

- overwrite:

  Allow replacement.

- manifest:

  Write a run manifest alongside the exported object.

## Value

Normalized output path invisibly.

## Examples

``` r
r <- terra::rast(ncols = 4, nrows = 3, xmin = 30, xmax = 34, ymin = -20, ymax = -17, crs = "EPSG:4326")
lay <- function(v, n) { x <- r; terra::values(x) <- v; names(x) <- n; x }
x <- land_data(climate = lay(1:12, "Pseason"), units = c(climate.Pseason = "mm"))
f <- tempfile(fileext = ".rds")
land_export(x, f, format = "rds", manifest = FALSE)
y <- land_import(f)
all.equal(terra::values(y$layers$climate), terra::values(x$layers$climate))
#> [1] TRUE
```
