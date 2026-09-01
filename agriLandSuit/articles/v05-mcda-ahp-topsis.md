# MCDA: AHP, TOPSIS, and Weight Sensitivity

``` r

library(agriLandSuit)
```

## AHP weights are decision information

Crop requirements describe biological or agronomic responses. AHP
weights instead describe the relative importance assigned to criteria in
a decision model. Version 0.5.0 deliberately keeps these concepts
separate.

``` r

cmp <- data.frame(
  criterion1 = c("climate", "climate", "soil"),
  criterion2 = c("soil", "terrain", "terrain"),
  value = c(1/2, 7, 3)
)
A <- ahp_matrix(c("climate", "soil", "terrain"), cmp)
w <- ahp_weights(A)
w$weights
w$consistency
```

A consistency ratio above the selected threshold is reported rather than
silently accepted.

## TOPSIS for alternatives

Criterion suitability scores should already have a scientifically
justified meaning before TOPSIS is used.

``` r

zones <- matrix(
  c(.9,.6,.7, .7,.8,.6, .5,.9,.8, .8,.4,.9),
  ncol = 3, byrow = TRUE,
  dimnames = list(paste0("zone", 1:4), c("climate","soil","terrain"))
)

t <- topsis_score(zones, weights = w$weights)
topsis_rank(t)
```

## Sensitivity to weights

``` r

s <- weight_sensitivity(
  zones,
  weights = w$weights,
  method = "weighted_arithmetic",
  span = 0.20,
  steps = 5
)
summary(s)
```

This is deterministic sensitivity analysis, not uncertainty propagation.
Probabilistic uncertainty is reserved for a later package version.
