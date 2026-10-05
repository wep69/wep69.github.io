# Monthly change factors between future and historical climate

\`delta\` factors (\`future - historical\`) suit temperature; \`ratio\`
factors (\`(future + offset) / (historical + offset)\`, optionally
clamped) suit rainfall and evapotranspiration. Optional coastal filling
and resampling to a study template are applied in that order, so that no
land cell of the template is lost.

## Usage

``` r
change_factors(
  future,
  historical,
  type = c("delta", "ratio"),
  offset = 1,
  clamp = NULL,
  fill = FALSE,
  template = NULL,
  iterations = 4L
)
```

## Arguments

- future, historical:

  \`SpatRaster\` objects with matching layers.

- type:

  \`delta\` or \`ratio\`.

- offset:

  Added to both terms of a ratio to avoid division by zero.

- clamp:

  Optional \`c(min, max)\` bounds for ratios.

- fill:

  Fill missing cells with \`fill_coastal()\` before resampling.

- template:

  Optional \`SpatRaster\` to resample to (bilinear).

- iterations:

  Passed to \`fill_coastal()\`.

## Value

\`SpatRaster\` of change factors.

## Examples

``` r
h <- terra::rast(ncols = 4, nrows = 3, nlyrs = 12, xmin = 30, xmax = 34, ymin = -20, ymax = -17)
terra::values(h) <- 100
fut <- h * 1.1; fut[1] <- NA
change_factors(fut, h, "ratio", clamp = c(0.2, 3), fill = TRUE)
#> class       : SpatRaster
#> size        : 3, 4, 12  (nrow, ncol, nlyr)
#> resolution  : 1, 1  (x, y)
#> extent      : 30, 34, -20, -17  (xmin, xmax, ymin, ymax)
#> coord. ref. : lon/lat WGS 84 (CRS84) (OGC:CRS84)
#> source(s)   : memory
#> names       : ratio_lyr.1, ratio_lyr.2, ratio_lyr.3, ratio_lyr.4, ratio_lyr.5, ratio_lyr.6, ...
#> min values  :     1.09901,     1.09901,     1.09901,     1.09901,     1.09901,     1.09901, ...
#> max values  :     1.09901,     1.09901,     1.09901,     1.09901,     1.09901,     1.09901, ...
change_factors(h + 2, h, "delta")
#> class       : SpatRaster
#> size        : 3, 4, 12  (nrow, ncol, nlyr)
#> resolution  : 1, 1  (x, y)
#> extent      : 30, 34, -20, -17  (xmin, xmax, ymin, ymax)
#> coord. ref. : lon/lat WGS 84 (CRS84) (OGC:CRS84)
#> source(s)   : memory
#> names       : delta_lyr.1, delta_lyr.2, delta_lyr.3, delta_lyr.4, delta_lyr.5, delta_lyr.6, ...
#> min values  :           2,           2,           2,           2,           2,           2, ...
#> max values  :           2,           2,           2,           2,           2,           2, ...
```
