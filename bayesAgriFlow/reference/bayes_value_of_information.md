# Compute expected value of perfect information

Compute EVPI as the expected utility obtainable with perfect knowledge
of the uncertain state minus the expected utility of the best current
action.

## Usage

``` r
bayes_value_of_information(utility, information_cost = 0)
```

## Arguments

- utility:

  A utility object or draws-by-actions utility matrix.

- information_cost:

  Optional cost subtracted from gross EVPI to report net EVPI.

## Value

A one-row data frame with current maximum expected utility,
perfect-information expected utility, EVPI, information cost, and net
EVPI.

## Examples

``` r
# Example 1
u <- cbind(A=rnorm(2000,100,20),B=rnorm(2000,105,25)); bayes_value_of_information(u)
#>   current_expected_utility expected_utility_with_perfect_information     evpi
#> 1                 104.4813                                  115.1913 10.71002
#>   information_cost net_evpi
#> 1                0 10.71002
# Example 2
bayes_value_of_information(u, information_cost=4)
#>   current_expected_utility expected_utility_with_perfect_information     evpi
#> 1                 104.4813                                  115.1913 10.71002
#>   information_cost net_evpi
#> 1                4 6.710022
# Example 3
bayes_value_of_information(bayes_utility(u))
#>   current_expected_utility expected_utility_with_perfect_information     evpi
#> 1                 104.4813                                  115.1913 10.71002
#>   information_cost net_evpi
#> 1                0 10.71002
```
