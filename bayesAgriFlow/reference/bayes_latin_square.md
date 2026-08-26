# Specify a Latin square design

Specify a Latin square design

## Usage

``` r
bayes_latin_square(data, response, treatment, row, column)
```

## Arguments

- data:

  A data frame.

- response:

  Response column name.

- treatment:

  Treatment column name.

- row:

  Row factor.

- column:

  Column factor.

## Value

A \`bayes_design\`.

## Examples

``` r
# Example 1
bayes_latin_square(bayes_teaching_data("latin_square"), "biomass",
                   "treatment", "row", "column")
#> <bayes_design>
#>   Type: Latin square design
#>   Response: biomass
#>   Family: Gaussian
#>   Observations: 25
#>   treatment: treatment
#>   row: row
#>   column: column
# Example 2
des <- bayes_latin_square(bayes_teaching_data("latin_square"), "biomass",
                          "treatment", "row", "column")
bayes_design_audit(des)
#> <bayes_design_audit>
#>   Design: Latin square design
#>   Status: PASS
# Example 3
des <- bayes_latin_square(bayes_teaching_data("latin_square"), "biomass",
                          "treatment", "row", "column")
bayes_plan(des, goal = c("estimation", "evidence"))
#> <bayes_plan>
#>   Design: Latin square design
#>   Audit: PASS
#>   Engine: native
#>   Reason: A Gaussian fixed-design teaching model can use the dependency-light conjugate engine.
#>   Goals: estimation, evidence
```
