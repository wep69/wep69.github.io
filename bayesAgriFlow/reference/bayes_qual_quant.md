# Specify a qualitative-by-quantitative regression

Specify a qualitative-by-quantitative regression

## Usage

``` r
bayes_qual_quant(data, response, factor, quantitative, block = NULL)
```

## Arguments

- data:

  A data frame.

- response:

  Numeric response column.

- factor:

  Qualitative factor.

- quantitative:

  Numeric predictor.

- block:

  Optional block factor.

## Value

A \`bayes_design\`.

## Examples

``` r
# Example 1: cultivar-specific dose curves
bayes_qual_quant(bayes_teaching_data("qual_quant"), "yield", "cultivar", "nitrogen")
#> <bayes_design>
#>   Type: Qualitative-by-quantitative regression
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 72
#>   treatment: cultivar
#>   quantitative: nitrogen
# Example 2: inspect the generated analysis plan
bayes_plan(bayes_qual_quant(bayes_teaching_data("qual_quant"), "yield", "cultivar", "nitrogen"))
#> <bayes_plan>
#>   Design: Qualitative-by-quantitative regression
#>   Audit: PASS
#>   Engine: native
#>   Reason: A Gaussian fixed-design teaching model can use the dependency-light conjugate engine.
#>   Goals: estimation, prediction
# Example 3: fit the interaction model
bayes_fit(bayes_qual_quant(bayes_teaching_data("qual_quant"), "yield",
          "cultivar", "nitrogen"), draws = 500)
#> <bayes_fit>
#>   Design: Qualitative-by-quantitative regression
#>   Family: Gaussian
#>   Engine: native
#>   Formula: yield ~ cultivar * nitrogen
#>   Posterior draws: 500
```
