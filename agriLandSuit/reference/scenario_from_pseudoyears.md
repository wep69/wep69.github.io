# Mean change factors from sampled pseudo-years

Pattern-scaled products such as the TerraClimate +2 and +4 degC
scenarios provide one future pseudo-year per historical year. When only
some years are downloaded, the mean monthly factor over the sampled
years estimates the climatological change; the between-year standard
deviation measures how stable that estimate is.

## Usage

``` r
scenario_from_pseudoyears(
  future,
  historical,
  type = c("delta", "ratio"),
  offset = 1,
  fill = TRUE,
  template = NULL,
  iterations = 4L
)
```

## Arguments

- future, historical:

  Lists of 12-layer \`SpatRaster\` objects, one per sampled year, in the
  same order.

- type:

  \`delta\` or \`ratio\`.

- offset, fill, template, iterations:

  As in \`change_factors()\`.

## Value

List with \`factor\` (12-layer \`SpatRaster\`) and \`stability\` (data
frame with month, mean over cells and between-year SD of the cell mean).

## Examples

``` r
mk <- function(v) { r <- terra::rast(ncols = 3, nrows = 3, nlyrs = 12, xmin = 0, xmax = 3, ymin = 0, ymax = 3)
  terra::values(r) <- v; r }
s <- scenario_from_pseudoyears(future = list(mk(22.1), mk(22.9)), historical = list(mk(20), mk(21)), type = "delta", fill = FALSE)
s$stability[1:3, ]
#>   month mean_over_cells sd_between_years
#> 1     1               2        0.1414214
#> 2     2               2        0.1414214
#> 3     3               2        0.1414214
```
