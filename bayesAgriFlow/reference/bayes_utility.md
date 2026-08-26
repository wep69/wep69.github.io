# Convert posterior outcomes into utility draws

Create action-specific posterior utility draws from posterior outcome
draws using a linear value-minus-cost rule or a user-supplied utility
function.

## Usage

``` r
bayes_utility(outcome_draws, value_per_unit = 1, action_costs = 0,
              utility_fun = NULL, actions = NULL)
```

## Arguments

- outcome_draws:

  Numeric draws-by-actions matrix.

- value_per_unit:

  Scalar converting outcomes to value for the default utility.

- action_costs:

  Scalar or one cost per action.

- utility_fun:

  Optional function receiving outcome, action, value_per_unit, and cost.

- actions:

  Optional action labels.

## Value

A `bayes_utility` object containing utility and original outcome draws
plus declared assumptions.

## Examples

``` r
# Example 1
y <- cbind(Control=rnorm(1000,5,.4), Fertilized=rnorm(1000,6,.5)); bayes_utility(y,1200,c(0,650))
#> bayesAgriFlow posterior utility draws
#>       action expected_utility
#> 1    Control         6031.866
#> 2 Fertilized         6524.826
# Example 2
bayes_utility(y, value_per_unit=1, action_costs=0)
#> bayesAgriFlow posterior utility draws
#>       action expected_utility
#> 1    Control         5.026555
#> 2 Fertilized         5.979022
# Example 3
bayes_utility(y, 1200, c(0, 650),
              utility_fun = function(outcome, action, value_per_unit, cost)
                sqrt(pmax(value_per_unit * outcome - cost, 0)))
#> bayesAgriFlow posterior utility draws
#>       action expected_utility
#> 1    Control         77.59615
#> 2 Fertilized         80.69060
```
