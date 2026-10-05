# Compute the decision margin between the two leading crops

Compute the decision margin between the two leading crops

## Usage

``` r
decision_margin(x, na_policy = c("propagate", "available"))
```

## Arguments

- x:

  Multi-crop comparison input.

- na_policy:

  Missing-value policy.

## Value

An \`agri_crop_decision_margin\` object. A zero margin explicitly
denotes a tie for the highest suitability score.

## Examples

``` r
z1 <- cbind(rain = c(.8, .6, .7, .4), temp = c(.9, .8, .9, .6))
z2 <- cbind(rain = c(.7, .65, .7, .5), temp = c(.8, .9, .9, .7))
z3 <- cbind(rain = c(.6, .55, .75, .5), temp = c(1, .9, .8, .8))
cmp <- compare_crops(list(maize = suit_aggregate(z1), bean = suit_aggregate(z2), sorghum = suit_aggregate(z3)))
decision_margin(cmp)$statistics
#>      best_score second_score margin
#> [1,]       0.80          0.7   0.10
#> [2,]       0.65          0.6   0.05
#> [3,]       0.75          0.7   0.05
#> [4,]       0.50          0.5   0.00
```
