# Aggregate criteria within each environmental domain

Aggregate criteria within each environmental domain

## Usage

``` r
domain_suitability(
  x,
  method = c("limiting", "weighted_arithmetic", "weighted_geometric"),
  weights = NULL,
  na_policy = c("propagate", "available"),
  epsilon = 1e-12
)
```

## Arguments

- x:

  An \`agri_criterion_set\` containing crop requirements from one or
  more domains.

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

An \`agri_domain_suitability\` object containing one composite per
represented domain. Criterion weights are subset and renormalized within
each domain.
