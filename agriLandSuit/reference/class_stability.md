# Quantify suitability-class stability

Quantify suitability-class stability

## Usage

``` r
class_stability(x)
```

## Arguments

- x:

  An \`agri_class_probability\` or \`agri_uncertainty_ensemble\` object.

## Value

An \`agri_class_stability\` with modal class, maximum class probability,
normalized entropy, and probability margin between the two leading
classes.

## Examples

``` r
set.seed(1)
mk <- function() suit_aggregate(cbind(rain = runif(3), temp = runif(3, 0.5, 1)))
members <- list(gcm1 = mk(), gcm2 = mk(), gcm3 = mk(), gcm4 = mk(), gcm5 = mk())
ens <- ensemble_suitability(members)
class_stability(ens)$stability
#>      modal_class max_probability   entropy margin
#> [1,]           2             0.6 0.4854753    0.2
#> [2,]           2             0.6 0.6854753    0.4
#> [3,]           3             0.6 0.6854753    0.4
```
