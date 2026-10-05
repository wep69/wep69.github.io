# Identify the second-ranked crop

Identify the second-ranked crop

## Usage

``` r
second_best_crop(
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

An \`agri_crop_selection\` object for the runner-up crop. When the top
score is tied and \`tie_policy = "NA"\`, the runner-up code remains
undefined.

## Examples

``` r
z1 <- cbind(rain = c(.8, .6, .7, .4), temp = c(.9, .8, .9, .6))
z2 <- cbind(rain = c(.7, .65, .7, .5), temp = c(.8, .9, .9, .7))
z3 <- cbind(rain = c(.6, .55, .75, .5), temp = c(1, .9, .8, .8))
cmp <- compare_crops(list(maize = suit_aggregate(z1), bean = suit_aggregate(z2), sorghum = suit_aggregate(z3)))
second_best_crop(cmp)$selection
#>      second_code second_score top_tied
#> [1,]           2          0.7        0
#> [2,]           1          0.6        0
#> [3,]           1          0.7        0
#> [4,]          NA          0.5        1
```
