# Specify a general Bayesian mixed-effects model

This wrapper keeps fixed effects and grouping structure explicit while
delegating posterior sampling to the brms/Stan backend.

## Usage

``` r
bayes_mixed(data, response, fixed, group = NULL,
                        random_slopes = NULL, nested = NULL, crossed = NULL,
                        residual_group = NULL,
                        family = c("gaussian", "student"))
```

## Arguments

- data:

  A data frame.

- response:

  Response column.

- fixed:

  One-sided fixed-effects formula or character vector.

- group:

  Character vector of random-intercept grouping factors.

- random_slopes:

  Named list mapping grouping factors to slope predictors.

- nested:

  Optional list of nested grouping chains.

- crossed:

  Optional character vector of crossed grouping factors.

- residual_group:

  Optional factor defining heterogeneous residual SDs.

- family:

  \`"gaussian"\` or \`"student"\` for this convenience wrapper.

## Value

A \`bayes_design\`.

## Examples

``` r
# Example 1: random intercept and random slope by block
d <- bayes_teaching_data("mixed")
bayes_mixed(d, "yield", ~ nitrogen, group = "block",
            random_slopes = list(block = "nitrogen"))
#> <bayes_design>
#>   Type: General mixed-effects model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 40
#>   fixed: nitrogen
#>   group: block
#>   random_slopes: block:[nitrogen]

# Example 2: Student-t mixed model
bayes_mixed(d, "yield", ~ nitrogen, group = "block", family = "student")
#> <bayes_design>
#>   Type: General mixed-effects model
#>   Response: yield
#>   Family: Student-t
#>   Observations: 40
#>   fixed: nitrogen
#>   group: block

# Example 3: heterogeneous residual variation by nitrogen class
dh <- bayes_teaching_data("heterogeneous")
bayes_mixed(dh, "yield", ~ treatment, group = "block",
            residual_group = "treatment")
#> <bayes_design>
#>   Type: General mixed-effects model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 32
#>   fixed: treatment
#>   group: block
#>   residual_group: treatment
```
