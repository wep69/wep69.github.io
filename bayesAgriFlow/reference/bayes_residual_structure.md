# Add or replace a heterogeneous residual-variance structure

Add or replace a heterogeneous residual-variance structure

## Usage

``` r
bayes_residual_structure(design, variance_by = NULL)
```

## Arguments

- design:

  A Gaussian or Student-t \`bayes_design\`.

- variance_by:

  Factor defining groups with distinct residual SDs. Use \`NULL\` to
  remove the heterogeneous structure.

## Value

An updated \`bayes_design\`.

## Examples

``` r
# Example 1: treatment-specific residual SDs
d <- bayes_teaching_data("heterogeneous")
des <- bayes_mixed(d, "yield", ~ treatment, group = "block")
bayes_residual_structure(des, "treatment")
#> <bayes_design>
#>   Type: General mixed-effects model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 32
#>   fixed: treatment
#>   group: block
#>   residual_group: treatment

# Example 2: remove the residual grouping
des2 <- bayes_residual_structure(des, "treatment")
bayes_residual_structure(des2, NULL)
#> <bayes_design>
#>   Type: General mixed-effects model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 32
#>   fixed: treatment
#>   group: block

# Example 3: inspect the resulting routing
bayes_plan(bayes_residual_structure(des, "treatment"))
#> <bayes_plan>
#>   Design: General mixed-effects model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
