# Specify a Bayesian spatial field-trial model

Part of the bayesAgriFlow 1.0.0 complex-systems workflow. The scientific
data structure is stored separately from the computational backend.

## Usage

``` r
bayes_spatial_field(data, response, x_coord, y_coord, fixed = NULL,
                    block = NULL, group = NULL,
                    family = c("gaussian", "student"),
                    cov = c("exp_quad", "matern32", "matern52",
                            "exponential"),
                    k = NULL, scale = TRUE)
```

## Arguments

- data:

  A data frame.

- response:

  Continuous response column.

- x_coord:

  Numeric field x coordinate.

- y_coord:

  Numeric field y coordinate.

- fixed:

  Optional fixed-effects specification.

- block:

  Optional block factor.

- group:

  Additional grouping factors.

- family:

  Gaussian or Student-t family.

- cov:

  Gaussian-process covariance kernel.

- k:

  Optional approximate GP basis size.

- scale:

  Scale spatial coordinates.

## Value

An auditable design object or posterior summary as described above.

## Details

Version 1.0.0 routes this structure to optional brms/Stan. Priors,
posterior predictive checks, sampler diagnostics, and sensitivity to
structural assumptions should be reported explicitly. Complex models are
not automatically validated by successful computation alone.

## Examples

``` r
# Example 1
d <- bayes_teaching_data("spatial")
bayes_spatial_field(d, "yield", "x", "y", ~ treatment, block = "block")
#> <bayes_design>
#>   Type: Spatial field-trial model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 80
#>   block: block
#>   fixed: treatment
#>   group: block
#>   x_coord: x
#>   y_coord: y

# Example 2
bayes_spatial_field(d, "yield", "x", "y", ~ treatment, family = "student")
#> <bayes_design>
#>   Type: Spatial field-trial model
#>   Response: yield
#>   Family: Student-t
#>   Observations: 80
#>   fixed: treatment
#>   x_coord: x
#>   y_coord: y

# Example 3
bayes_plan(bayes_spatial_field(d, "yield", "x", "y", ~ treatment, k = 30))
#> <bayes_plan>
#>   Design: Spatial field-trial model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
