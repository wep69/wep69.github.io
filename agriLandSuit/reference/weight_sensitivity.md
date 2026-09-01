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
