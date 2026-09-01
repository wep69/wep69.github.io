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
