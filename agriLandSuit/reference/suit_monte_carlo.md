# Propagate declared score and weight uncertainty by Monte Carlo simulation

Suitability scores are perturbed with Beta distributions whose
expectation is the original score; exact 0 and 1 remain exact
boundaries. Decision weights can be perturbed multiplicatively with
lognormal factors and are normalized after each draw. The function does
not invent uncertainty: users must provide an \`uncertainty_spec()\`
object.

## Usage

``` r
suit_monte_carlo(
  x,
  uncertainty,
  n = 1000L,
  seed = 1L,
  method = c("limiting", "weighted_arithmetic", "weighted_geometric"),
  weights = NULL,
  constraints = NULL,
  na_policy = c("propagate", "available"),
  epsilon = 1e-12,
  breaks = c(0, 0.25, 0.5, 0.75, 1),
  labels = c("N", "S3", "S2", "S1"),
  engine = c("r", "python", "auto")
)
```

## Arguments

- x:

  Criterion-score input accepted by \`suit_aggregate()\`.

- uncertainty:

  An \`agri_uncertainty_spec\` matching the criteria in \`x\`.

- n:

  Number of Monte Carlo draws (at least 20).

- seed:

  Integer random seed.

- method, weights, na_policy, epsilon:

  Passed to aggregation.

- constraints:

  Optional \`agri_constraint_effects\`; raster inputs only.

- breaks, labels:

  Suitability class definition stored for downstream class
  probabilities.

- engine:

  \`r\`, \`python\`, or \`auto\`. Python uses NumPy and is available for
  numeric matrix/data-frame inputs only. Raster simulation remains
  \`terra\`-native.

## Value

An \`agri_uncertainty_ensemble\` containing Monte Carlo suitability
draws.
