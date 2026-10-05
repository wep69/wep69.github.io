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

## Examples

``` r
z <- matrix(c(.35, .65, .55, .75, .45, .85), nrow = 3, byrow = TRUE, dimnames = list(NULL, c("climate", "soil")))
u <- uncertainty_spec(z, score_concentration = 100, weight_log_sd = 0.05)
mc <- suit_monte_carlo(z, u, n = 200, seed = 7, method = "weighted_arithmetic", weights = c(climate = 0.6, soil = 0.4))
uncertainty_summary(mc)$statistics
#>           mean         sd       p05       p50       p95 n_available
#> [1,] 0.4754946 0.03466203 0.4135038 0.4752664 0.5296256         200
#> [2,] 0.6305752 0.03373160 0.5766012 0.6300972 0.6911377         200
#> [3,] 0.6104655 0.03338793 0.5523146 0.6097608 0.6654458         200
class_probability(mc)$probability
#>      P_N  P_S3  P_S2 P_S1
#> [1,]   0 0.755 0.245    0
#> [2,]   0 0.000 1.000    0
#> [3,]   0 0.000 1.000    0
```
