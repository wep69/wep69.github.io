# Summarize a Monte Carlo or scenario ensemble

Summarize a Monte Carlo or scenario ensemble

## Usage

``` r
uncertainty_summary(x, probs = c(0.05, 0.5, 0.95))
```

## Arguments

- x:

  An \`agri_uncertainty_ensemble\`.

- probs:

  Quantile probabilities. Defaults to P05, P50, and P95.

## Value

An \`agri_uncertainty_summary\` with mean, weighted SD, quantiles, and
number of available members for each spatial/numeric unit.

## Examples

``` r
set.seed(1)
mk <- function() suit_aggregate(cbind(rain = runif(3), temp = runif(3, 0.5, 1)))
members <- list(gcm1 = mk(), gcm2 = mk(), gcm3 = mk(), gcm4 = mk(), gcm5 = mk())
ens <- ensemble_suitability(members)
uncertainty_summary(ens, probs = c(0.1, 0.5, 0.9))$statistics
#>           mean        sd        p10       p50       p90 n_available
#> [1,] 0.4261361 0.1626140 0.26550866 0.3800352 0.6870228           5
#> [2,] 0.5045548 0.1613894 0.37212390 0.3861141 0.7774452           5
#> [3,] 0.5014282 0.2555914 0.01339033 0.5728534 0.7698414           5
```
