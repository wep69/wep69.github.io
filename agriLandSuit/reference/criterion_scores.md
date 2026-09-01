# Extract criterion suitability scores

Extract criterion suitability scores

## Usage

``` r
criterion_scores(x, stack = TRUE)
```

## Arguments

- x:

  An \`agri_criterion_set\`.

- stack:

  If TRUE and every score is a SpatRaster with matching geometry, return
  one multi-layer SpatRaster; otherwise return a named list.

## Value

A SpatRaster or named list.
