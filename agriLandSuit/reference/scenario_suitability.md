# Evaluate land suitability across climate scenarios

Evaluate land suitability across climate scenarios

## Usage

``` r
scenario_suitability(
  x,
  crop,
  method = c("limiting", "weighted_arithmetic", "weighted_geometric"),
  weights = NULL,
  constraints = NULL,
  engine = c("r", "python", "auto"),
  strict = TRUE,
  strict_units = TRUE,
  na_policy = c("propagate", "available"),
  epsilon = 1e-12,
  breaks = c(0, 0.25, 0.5, 0.75, 1),
  labels = c("N", "S3", "S2", "S1")
)
```

## Arguments

- x:

  An \`agri_scenario_set\`.

- crop:

  An \`agri_crop_profile\`.

- method:

  Aggregation method passed to \`land_suitability()\`.

- weights:

  Optional criterion weights shared across scenarios.

- constraints:

  NULL, one \`agri_constraint_effects\` object shared across scenarios,
  or a named list keyed by scenario ID.

- engine:

  Criterion-scoring engine: \`r\`, \`python\`, or \`auto\`.

- strict:

  Require every crop requirement to have a matching land layer.

- strict_units:

  Require source-layer units to match crop requirements.

- na_policy:

  Missing-value policy passed to \`land_suitability()\`.

- epsilon:

  Numerical floor passed to geometric aggregation.

- breaks, labels:

  Suitability classification specification.

## Value

An \`agri_scenario_suitability\` object.
