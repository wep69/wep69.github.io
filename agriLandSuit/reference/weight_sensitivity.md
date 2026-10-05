# One-at-a-time sensitivity analysis for criterion weights

One-at-a-time sensitivity analysis for criterion weights

## Usage

``` r
weight_sensitivity(
  x,
  weights,
  method = c("weighted_arithmetic", "weighted_geometric"),
  span = 0.2,
  steps = 5L,
  na_policy = c("propagate", "available"),
  keep_scores = FALSE
)
```

## Arguments

- x:

  Criterion score object accepted by \`suit_aggregate()\`.

- weights:

  Baseline criterion weights.

- method:

  \`weighted_arithmetic\` or \`weighted_geometric\`.

- span:

  Relative perturbation around each baseline weight, from 0 to \<1.

- steps:

  Number of factors evaluated between \`1-span\` and \`1+span\`.

- na_policy:

  Missing-value policy passed to \`suit_aggregate()\`.

- keep_scores:

  If TRUE, retain every scenario score object.

## Value

An \`agri_weight_sensitivity\` object with scenario-level change
metrics.

## Examples

``` r
z <- matrix(c(.9, .6, .7, .7, .8, .6, .5, .9, .8, .8, .4, .9), ncol = 3, byrow = TRUE,
            dimnames = list(paste0("zone", 1:4), c("climate", "soil", "terrain")))
ws <- weight_sensitivity(z, weights = c(climate = 0.4, soil = 0.35, terrain = 0.25), steps = 5)
head(ws$scenarios)
#>   criterion factor    weight   mean_change mean_abs_change max_abs_change
#> 1   climate    0.8 0.3478261 -0.0009782609     0.010760870    0.018695652
#> 2   climate    0.9 0.3750000 -0.0004687500     0.005156250    0.008958333
#> 3   climate    1.0 0.4000000  0.0000000000     0.000000000    0.000000000
#> 4   climate    1.1 0.4230769  0.0004326923     0.004759615    0.008269231
#> 5   climate    1.2 0.4444444  0.0008333333     0.009166667    0.015925926
#> 6      soil    0.8 0.3010753  0.0029166667     0.013266129    0.021451613
```
