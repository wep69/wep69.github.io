# Rank crops by suitability

Rank crops by suitability

## Usage

``` r
crop_rank(
  x,
  ties_method = c("average", "min", "max"),
  na_policy = c("propagate", "available")
)
```

## Arguments

- x:

  An \`agri_multi_crop_comparison\` or a named list accepted by
  \`compare_crops()\`.

- ties_method:

  Ranking method for equal scores: \`average\`, \`min\`, or \`max\`.

- na_policy:

  \`propagate\` makes the complete local ranking missing when any crop
  is missing; \`available\` ranks only observed crops.

## Value

An \`agri_crop_rank\` object with rank 1 representing highest
suitability.

## Examples

``` r
z1 <- cbind(rain = c(.8, .6, .7, .4), temp = c(.9, .8, .9, .6))
z2 <- cbind(rain = c(.7, .65, .7, .5), temp = c(.8, .9, .9, .7))
z3 <- cbind(rain = c(.6, .55, .75, .5), temp = c(1, .9, .8, .8))
cmp <- compare_crops(list(maize = suit_aggregate(z1), bean = suit_aggregate(z2), sorghum = suit_aggregate(z3)))
crop_rank(cmp)$rank
#>      maize bean sorghum
#> [1,]   1.0  2.0     3.0
#> [2,]   2.0  1.0     3.0
#> [3,]   2.5  2.5     1.0
#> [4,]   3.0  1.5     1.5
```
