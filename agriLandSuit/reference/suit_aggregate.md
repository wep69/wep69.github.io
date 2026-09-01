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
