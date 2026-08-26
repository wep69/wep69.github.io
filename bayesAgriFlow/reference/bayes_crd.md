# Specify a completely randomized design

Specify a completely randomized design

## Usage

``` r
bayes_crd(data, response, treatment)
```

## Arguments

- data:

  A data frame.

- response:

  Response column name.

- treatment:

  Treatment column name.

## Value

A \`bayes_design\`.

## Examples

``` r
# Example 1
bayes_crd(bayes_teaching_data("crd"), "yield", "treatment")
#> <bayes_design>
#>   Type: Completely randomized design
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 32
#>   treatment: treatment
# Example 2
d <- bayes_teaching_data("crd"); d$treatment <- relevel(d$treatment, "Control")
bayes_crd(d, "yield", "treatment")
#> <bayes_design>
#>   Type: Completely randomized design
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 32
#>   treatment: treatment
# Example 3
des <- bayes_crd(bayes_teaching_data("crd"), "yield", "treatment")
bayes_design_audit(des)
#> <bayes_design_audit>
#>   Design: Completely randomized design
#>   Status: PASS
```
