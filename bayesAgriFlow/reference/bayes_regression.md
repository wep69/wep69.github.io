# Specify a quantitative-factor regression

Specify a quantitative-factor regression

## Usage

``` r
bayes_regression(data, response, quantitative)
```

## Arguments

- data:

  A data frame.

- response:

  Numeric response column.

- quantitative:

  Numeric predictor column.

## Value

A \`bayes_design\`.

## Examples

``` r
# Example 1: simple dose regression
bayes_regression(bayes_teaching_data("dose"), "yield", "nitrogen")
#> <bayes_design>
#>   Type: Quantitative-factor regression
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 30
#>   quantitative: nitrogen
# Example 2: inspect the design audit
bayes_design_audit(bayes_regression(bayes_teaching_data("dose"), "yield", "nitrogen"))
#> <bayes_design_audit>
#>   Design: Quantitative-factor regression
#>   Status: PASS
# Example 3: fit with the native teaching engine
bayes_fit(bayes_regression(bayes_teaching_data("dose"), "yield", "nitrogen"), draws = 500)
#> <bayes_fit>
#>   Design: Quantitative-factor regression
#>   Family: Gaussian
#>   Engine: native
#>   Formula: yield ~ nitrogen
#>   Posterior draws: 500
```
