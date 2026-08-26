# Choose a Bayes action from posterior utility draws

Compute expected utility, probability that each action is optimal,
expected regret, and the action maximizing posterior expected utility.

## Usage

``` r
bayes_decision(utility, actions = NULL)
```

## Arguments

- utility:

  A `bayes_utility` object or numeric draws-by-actions utility matrix.

- actions:

  Optional labels when utility is a plain matrix.

## Value

A `bayes_decision` object with an ordered decision table and draw-level
regret quantities.

## Examples

``` r
# Example 1
u <- cbind(A=rnorm(1000,100,15),B=rnorm(1000,110,30)); bayes_decision(u)
#> bayesAgriFlow Bayesian decision
#> Recommended action: B
#>  action expected_utility probability_optimal expected_regret
#>       B        111.84709               0.635        8.411124
#>       A         99.66541               0.365       20.592795
# Example 2
y <- cbind(Control=rnorm(1000,5,.4), N100=rnorm(1000,6,.5))
bayes_decision(bayes_utility(y, 1200, c(0, 500)))
#> bayesAgriFlow Bayesian decision
#> Recommended action: N100
#>   action expected_utility probability_optimal expected_regret
#>     N100         6704.969               0.817        72.44159
#>  Control         5997.675               0.183       779.73554
# Example 3
bayes_decision(u)$recommended_action
#> [1] "B"
```
