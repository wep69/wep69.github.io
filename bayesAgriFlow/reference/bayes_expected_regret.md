# Compute expected regret for each action

Calculate posterior expected opportunity loss for each action under the
same utility matrix used for a Bayesian decision.

## Usage

``` r
bayes_expected_regret(object)
```

## Arguments

- object:

  A `bayes_decision`, `bayes_utility`, or utility matrix.

## Value

A data frame ordered from smallest to largest expected regret.

## Examples

``` r
# Example 1
u <- cbind(A=rnorm(1000,10,2),B=rnorm(1000,11,4)); bayes_expected_regret(bayes_decision(u))
#>   action expected_regret
#> 2      B        1.405458
#> 1      A        2.138275
# Example 2
bayes_expected_regret(u)
#>   action expected_regret
#> 2      B        1.405458
#> 1      A        2.138275
# Example 3
head(bayes_expected_regret(u),1)
#>   action expected_regret
#> 2      B        1.405458
```
