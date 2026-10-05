# Rank alternatives from TOPSIS preference scores

Rank alternatives from TOPSIS preference scores

## Usage

``` r
topsis_rank(x, ...)
```

## Arguments

- x:

  An \`agri_topsis\` object or an input accepted by \`topsis_score()\`.

- ...:

  Passed to \`topsis_score()\` when \`x\` is not already an
  \`agri_topsis\` object.

## Value

For vector results, a data.frame ordered from best to worst. Raster
results return a SpatRaster of closeness scores because materializing a
global cell rank can be prohibitively large.

## Examples

``` r
z <- matrix(c(.9, .6, .7, .7, .8, .6, .5, .9, .8, .8, .4, .9), ncol = 3, byrow = TRUE,
            dimnames = list(paste0("zone", 1:4), c("climate", "soil", "terrain")))
topsis_rank(topsis_score(z, weights = c(climate = 0.4, soil = 0.35, terrain = 0.25)))
#>   alternative preference rank
#> 1       zone1  0.5951949    1
#> 2       zone2  0.5944708    2
#> 3       zone3  0.5411189    3
#> 4       zone4  0.4267614    4
```
