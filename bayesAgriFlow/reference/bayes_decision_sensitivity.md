# Evaluate treatment decisions over an outcome-value grid

Recompute treatment-level linear-utility decisions over a grid of output
values while holding the fitted posterior and action costs fixed.

## Usage

``` r
bayes_decision_sensitivity(fit, factor, value_grid, costs = 0)
```

## Arguments

- fit:

  A compatible `bayes_fit` object.

- factor:

  Treatment factor defining actions.

- value_grid:

  Numeric vector of outcome values to evaluate.

- costs:

  Scalar or one action cost per treatment.

## Value

A data frame showing the recommended action and associated decision
summaries at each value in the grid.

## Examples

``` r
# Example 1
fit <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
                 draws = 500, seed = 1)
bayes_decision_sensitivity(fit, "treatment", c(500, 1000, 1500))
#>   value_per_unit recommended_action expected_utility probability_optimal
#> 1            500               BioC         3478.778               0.924
#> 2           1000               BioC         6957.556               0.924
#> 3           1500               BioC        10436.334               0.924
#>   expected_regret      evpi
#> 1        3.603117  3.603117
#> 2        7.206233  7.206233
#> 3       10.809350 10.809350
# Example 2
bayes_decision_sensitivity(fit,"treatment",c(750,1000),costs=c(0,120,240,360))
#>   value_per_unit recommended_action expected_utility probability_optimal
#> 1            750               BioC         4978.167               0.714
#> 2           1000               BioC         6717.556               0.778
#>   expected_regret     evpi
#> 1        26.02990 26.02990
#> 2        24.59483 24.59483
# Example 3
head(bayes_decision_sensitivity(fit,"treatment",seq(500,1500,250)))
#>   value_per_unit recommended_action expected_utility probability_optimal
#> 1            500               BioC         3478.778               0.924
#> 2            750               BioC         5218.167               0.924
#> 3           1000               BioC         6957.556               0.924
#> 4           1250               BioC         8696.945               0.924
#> 5           1500               BioC        10436.334               0.924
#>   expected_regret      evpi
#> 1        3.603117  3.603117
#> 2        5.404675  5.404675
#> 3        7.206233  7.206233
#> 4        9.007792  9.007792
#> 5       10.809350 10.809350
```
