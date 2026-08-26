# Specify polynomial regression for a quantitative factor

Specify polynomial regression for a quantitative factor

## Usage

``` r
bayes_polynomial(data, response, quantitative, degree = 2L)
```

## Arguments

- data:

  A data frame.

- response:

  Numeric response column.

- quantitative:

  Numeric predictor column.

- degree:

  Polynomial degree from 1 to 3.

## Value

A \`bayes_design\`.

## Examples

``` r
# Example 1: quadratic dose response
bayes_polynomial(bayes_teaching_data("dose"), "yield", "nitrogen", degree = 2)
#> <bayes_design>
#>   Type: Polynomial regression
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 30
#>   quantitative: nitrogen
# Example 2: cubic model
bayes_polynomial(bayes_teaching_data("dose"), "yield", "nitrogen", degree = 3)
#> <bayes_design>
#>   Type: Polynomial regression
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 30
#>   quantitative: nitrogen
# Example 3: fit a quadratic model
bayes_fit(bayes_polynomial(bayes_teaching_data("dose"), "yield", "nitrogen", 2), draws = 500)
#> <bayes_fit>
#>   Design: Polynomial regression
#>   Family: Gaussian
#>   Engine: native
#>   Formula: yield ~ nitrogen + I(nitrogen^2)
#>   Posterior draws: 500
```
