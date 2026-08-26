# Bayesian economic optimum from posterior response curves

Bayesian economic optimum from posterior response curves

## Usage

``` r
bayes_economic_optimum(fit, domain, output_price, input_cost_per_unit,
                                    fixed_cost = 0, grid = 401L,
                                    factor_level = NULL, level = 0.95)
```

## Arguments

- fit:

  A fitted quantitative response-curve model.

- domain:

  Numeric optimization domain.

- output_price:

  Revenue per response unit.

- input_cost_per_unit:

  Linear cost per unit of the quantitative input.

- fixed_cost:

  Optional fixed cost.

- grid:

  Number of grid points.

- factor_level:

  Optional qualitative-factor level.

- level:

  Credible interval mass.

## Value

A \`bayes_optimum\` object whose response field is net return.

## Examples

``` r
# Example 1: economic nitrogen optimum
f <- bayes_fit(bayes_polynomial(bayes_teaching_data("dose"), "yield", "nitrogen", 2), draws = 600)
bayes_economic_optimum(f, c(0, 200), output_price = 1.2, input_cost_per_unit = 0.8)
#> <bayes_optimum>
#>  optimum_median optimum_lower optimum_upper net_return_median net_return_lower
#>               0             0             0           6.41605          5.80415
#>  net_return_upper p_positive_net_return factor_level
#>          7.001762                     1         <NA>
# Example 2: include a fixed operational cost
bayes_economic_optimum(f, c(0, 200), 1.2, 0.8, fixed_cost = 20)
#> <bayes_optimum>
#>  optimum_median optimum_lower optimum_upper net_return_median net_return_lower
#>               0             0             0         -13.58395        -14.19585
#>  net_return_upper p_positive_net_return factor_level
#>         -12.99824                     0         <NA>
# Example 3: use a finer grid
bayes_economic_optimum(f, c(0, 200), 1.2, 0.8, grid = 501)
#> <bayes_optimum>
#>  optimum_median optimum_lower optimum_upper net_return_median net_return_lower
#>               0             0             0           6.41605          5.80415
#>  net_return_upper p_positive_net_return factor_level
#>          7.001762                     1         <NA>
```
