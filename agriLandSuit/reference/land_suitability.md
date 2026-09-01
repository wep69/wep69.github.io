# Build a classified composite land-suitability result

Build a classified composite land-suitability result

## Usage

``` r
land_suitability(
  criteria,
  method = c("limiting", "weighted_arithmetic", "weighted_geometric"),
  weights = NULL,
  constraints = NULL,
  na_policy = c("propagate", "available"),
  epsilon = 1e-12,
  breaks = c(0, 0.25, 0.5, 0.75, 1),
  labels = c("N", "S3", "S2", "S1")
)
```

## Arguments

- criteria:

  Criterion-score input accepted by \`suit_aggregate()\`.

- method:

  One of \`limiting\`, \`weighted_arithmetic\`, or
  \`weighted_geometric\`.

- weights:

  Optional criterion weights. Equal weights are used by default.

- constraints:

  Optional \`agri_constraint_effects\`. Effects are applied after
  criterion aggregation, preserving the unconstrained composite.

- na_policy:

  \`propagate\` keeps missingness explicit; \`available\` uses available
  criteria and renormalizes weights where required.

- epsilon:

  Positive numerical floor used only inside logarithms for the geometric
  calculation; exact zero suitability is restored to zero.

- breaks, labels:

  Classification specification passed to \`suit_classify()\`.

## Value

An \`agri_land_suitability\` object inheriting from
\`agri_suitability\`.
