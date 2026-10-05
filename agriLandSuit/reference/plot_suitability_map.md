# Map suitability scores or classes

Map suitability scores or classes

## Usage

``` r
plot_suitability_map(
  x,
  type = c("score", "class"),
  boundary = NULL,
  breaks = c(0, 0.25, 0.5, 0.75, 1),
  labels = c("N", "S3", "S2", "S1"),
  closed = "left",
  titles = NULL
)
```

## Arguments

- x:

  \`SpatRaster\` (one or more layers) or \`agri_suitability\` with
  raster score. Several layers are drawn as facets.

- type:

  \`score\` (continuous) or \`class\`.

- boundary:

  Optional \`SpatVector\` or \`sf\` object drawn on top.

- breaks, labels, closed:

  Class definition for \`type = "class"\`.

- titles:

  Optional facet titles (named by layer).

## Value

A \`ggplot\` object.

## Examples

``` r
r <- terra::rast(ncols = 20, nrows = 15, xmin = 30, xmax = 32, ymin = -20, ymax = -18.5, crs = "EPSG:4326")
terra::values(r) <- seq(0, 1, length.out = terra::ncell(r)); names(r) <- "maize"
if (requireNamespace("ggplot2", quietly = TRUE)) {
  plot_suitability_map(r)
  plot_suitability_map(r, type = "class")
}
```
