# Create a suitability ensemble from scenario/model results

Create a suitability ensemble from scenario/model results

## Usage

``` r
ensemble_suitability(x, members = NULL, weights = NULL)
```

## Arguments

- x:

  An \`agri_scenario_suitability\` object or a list of
  \`agri_suitability\` objects.

- members:

  Optional scenario/member IDs. By default, scenario ensembles use all
  non-baseline members when possible.

- weights:

  Optional non-negative member weights. Equal weights are the default.

## Value

An \`agri_uncertainty_ensemble\`.

## Examples

``` r
set.seed(1)
mk <- function() suit_aggregate(cbind(rain = runif(3), temp = runif(3, 0.5, 1)))
members <- list(gcm1 = mk(), gcm2 = mk(), gcm3 = mk(), gcm4 = mk(), gcm5 = mk())
ens <- ensemble_suitability(members)
ens
#> <agri_uncertainty_ensemble> 5 members; kind: model 
uncertainty_summary(ens)$statistics
#>           mean        sd        p05       p50       p95 n_available
#> [1,] 0.4261361 0.1626140 0.26550866 0.3800352 0.6870228           5
#> [2,] 0.5045548 0.1613894 0.37212390 0.3861141 0.7774452           5
#> [3,] 0.5014282 0.2555914 0.01339033 0.5728534 0.7698414           5
```
