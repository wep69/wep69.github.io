# Specify a qualitative factorial design

Specify a qualitative factorial design

## Usage

``` r
bayes_factorial(data, response, factors, block = NULL)
```

## Arguments

- data:

  A data frame.

- response:

  Response column name.

- factors:

  Character vector of qualitative factorial predictors.

- block:

  Optional block factor.

## Value

A \`bayes_design\`.

## Examples

``` r
# Example 1
bayes_factorial(bayes_teaching_data("factorial"), "yield",
                c("cultivar", "fertilizer"), "block")
#> <bayes_design>
#>   Type: Qualitative factorial design
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 24
#>   block: block
#>   factors: cultivar, fertilizer
# Example 2
des <- bayes_factorial(bayes_teaching_data("factorial"), "yield",
                       c("cultivar", "fertilizer"), "block")
bayes_design_audit(des)
#> <bayes_design_audit>
#>   Design: Qualitative factorial design
#>   Status: PASS
# Example 3
des <- bayes_factorial(bayes_teaching_data("factorial"), "yield",
                       c("cultivar", "fertilizer"), "block")
bayes_plan(des)
#> <bayes_plan>
#>   Design: Qualitative factorial design
#>   Audit: PASS
#>   Engine: native
#>   Reason: A Gaussian fixed-design teaching model can use the dependency-light conjugate engine.
#>   Goals: estimation, prediction
```
