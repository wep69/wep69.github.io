# Aggregate criterion suitability scores

Aggregate criterion suitability scores

## Usage

``` r
suit_aggregate(
  x,
  method = c("limiting", "weighted_arithmetic", "weighted_geometric"),
  weights = NULL,
  na_policy = c("propagate", "available"),
  epsilon = 1e-12
)
```

## Arguments

- x:

  \`agri_criterion_set\`, multi-layer \`SpatRaster\`, numeric matrix, or
  numeric data.frame. Columns/layers are criteria and rows/cells are
  units.

- method:

  One of \`limiting\`, \`weighted_arithmetic\`, or
  \`weighted_geometric\`.

- weights:

  Optional criterion weights. Equal weights are used by default.

- na_policy:

  \`propagate\` keeps missingness explicit; \`available\` uses available
  criteria and renormalizes weights where required.

- epsilon:

  Positive numerical floor used only inside logarithms for the geometric
  calculation; exact zero suitability is restored to zero.

## Value

An \`agri_suitability\` object.

## Examples

``` r
z <- cbind(rain = c(0.9, 0.6, 0.3, 0.1), temp = c(1, 0.7, 0.8, 0.5))
suit_aggregate(z)$score
#> [1] 0.9 0.6 0.3 0.1
suit_aggregate(z, method = "weighted_arithmetic", weights = c(rain = 0.7, temp = 0.3))$score
#> [1] 0.93 0.63 0.45 0.22
suit_aggregate(z, method = "weighted_geometric")$score
#> [1] 0.9486833 0.6480741 0.4898979 0.2236068
```
