# Specify a randomized complete block design

Specify a randomized complete block design

## Usage

``` r
bayes_rcbd(data, response, treatment, block)
```

## Arguments

- data:

  A data frame.

- response:

  Response column name.

- treatment:

  Treatment column name.

- block:

  Block column name.

## Value

A \`bayes_design\`.

## Examples

``` r
# Example 1
bayes_rcbd(bayes_teaching_data("rcbd"), "yield", "cultivar", "block")
#> <bayes_design>
#>   Type: Randomized complete block design
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 20
#>   treatment: cultivar
#>   block: block
# Example 2
des <- bayes_rcbd(bayes_teaching_data("rcbd"), "yield", "cultivar", "block")
bayes_plan(des)
#> <bayes_plan>
#>   Design: Randomized complete block design
#>   Audit: PASS
#>   Engine: native
#>   Reason: A Gaussian fixed-design teaching model can use the dependency-light conjugate engine.
#>   Goals: estimation, prediction
# Example 3
des <- bayes_rcbd(bayes_teaching_data("rcbd"), "yield", "cultivar", "block")
bayes_design_audit(des)
#> <bayes_design_audit>
#>   Design: Randomized complete block design
#>   Status: PASS
```
