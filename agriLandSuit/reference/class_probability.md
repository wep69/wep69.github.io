# Compute probability of each suitability class

Compute probability of each suitability class

## Usage

``` r
class_probability(x, breaks = x$breaks, labels = x$labels)
```

## Arguments

- x:

  An \`agri_uncertainty_ensemble\`.

- breaks, labels:

  Optional class definition. Defaults to the ensemble's stored
  classification.

## Value

An \`agri_class_probability\` object.

## Examples

``` r
set.seed(1)
mk <- function() suit_aggregate(cbind(rain = runif(3), temp = runif(3, 0.5, 1)))
members <- list(gcm1 = mk(), gcm2 = mk(), gcm3 = mk(), gcm4 = mk(), gcm5 = mk())
ens <- ensemble_suitability(members)
class_probability(ens)$probability
#>      P_N P_S3 P_S2 P_S1
#> [1,] 0.0  0.6  0.4  0.0
#> [2,] 0.0  0.6  0.2  0.2
#> [3,] 0.2  0.0  0.6  0.2
```
