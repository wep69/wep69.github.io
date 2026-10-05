# Map of the limiting criterion or domain

Identifies, for each unit, the criterion (or environmental domain) with
the lowest score. Units whose minimum score is at or above
\`none_threshold\` are coded 0 (\`none\`): nothing limits them.

## Usage

``` r
limiting_map(
  x,
  level = c("criterion", "domain"),
  none_threshold = 0.999,
  method = "limiting",
  na_policy = c("propagate", "available")
)
```

## Arguments

- x:

  An \`agri_criterion_set\`, \`agri_domain_suitability\`, multi-layer
  \`SpatRaster\` or score matrix.

- level:

  \`criterion\` or \`domain\`. With \`domain\`, an
  \`agri_criterion_set\` is first aggregated with
  \`domain_suitability()\`.

- none_threshold:

  Score at or above which no factor is limiting.

- method:

  Aggregation within domains when \`level = "domain"\`.

- na_policy:

  Passed to \`limiting_factor()\`: \`propagate\` (units with any missing
  score are NA) or \`available\` (minimum over available scores).

## Value

An \`agri_limiting_map\` list with \`index\` (raster or integer vector),
\`key\` (code, name) and \`shares\` (percentage of units per code).

## Examples

``` r
r <- terra::rast(ncols = 3, nrows = 2, nlyrs = 3, xmin = 30, xmax = 33, ymin = -20, ymax = -18, crs = "EPSG:4326")
terra::values(r) <- cbind(c(1, .4, .9, 1, .2, .7), c(1, .8, .3, 1, .6, .9), c(1, .9, .8, .5, .7, .1))
names(r) <- c("climate", "soil", "water")
lm <- limiting_map(r, level = "domain")
lm
#> <agri_limiting_map> level: domain ; none if minimum >= 0.999 
#>  code    name share_pct
#>     0    none  16.66667
#>     1 climate  33.33333
#>     2    soil  16.66667
#>     3   water  33.33333
terra::values(lm$index)
#>      criterion_index
#> [1,]               0
#> [2,]               1
#> [3,]               2
#> [4,]               3
#> [5,]               1
#> [6,]               3
```
