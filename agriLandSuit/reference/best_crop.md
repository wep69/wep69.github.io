# Identify the best crop at each unit or raster cell

Identify the best crop at each unit or raster cell

## Usage

``` r
best_crop(
  x,
  tie_policy = c("NA", "first"),
  na_policy = c("propagate", "available")
)
```

## Arguments

- x:

  Multi-crop comparison input.

- tie_policy:

  \`NA\` (default) leaves the winning crop undefined when two or more
  crops share the maximum score; \`first\` uses stable crop order but
  still reports the number tied.

- na_policy:

  Missing-value policy.

## Value

An \`agri_crop_selection\` object with best crop code, score, and number
of crops tied for first.

## Examples

``` r
z1 <- cbind(rain = c(.8, .6, .7, .4), temp = c(.9, .8, .9, .6))
z2 <- cbind(rain = c(.7, .65, .7, .5), temp = c(.8, .9, .9, .7))
z3 <- cbind(rain = c(.6, .55, .75, .5), temp = c(1, .9, .8, .8))
cmp <- compare_crops(list(maize = suit_aggregate(z1), bean = suit_aggregate(z2), sorghum = suit_aggregate(z3)))
best_crop(cmp)$selection
#>      best_code best_score n_tied
#> [1,]         1       0.80      1
#> [2,]         2       0.65      1
#> [3,]         3       0.75      1
#> [4,]        NA       0.50      2
```
