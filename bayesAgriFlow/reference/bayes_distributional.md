# Specify a distributional Bayesian regression

Part of the bayesAgriFlow 1.0.0 complex-systems workflow. The scientific
data structure is stored separately from the computational backend.

## Usage

``` r
bayes_distributional(data, response, fixed = NULL, distributional,
                     group = NULL,
                     family = c("gaussian", "student", "negbinomial",
                                "gamma", "beta"))
```

## Arguments

- data:

  A data frame.

- response:

  Response column.

- fixed:

  Main location-model specification.

- distributional:

  Named list of formulas for distributional parameters.

- group:

  Optional grouping factors.

- family:

  Response family.

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
d <- bayes_teaching_data("distributional")
bayes_distributional(d, "yield", ~ irrigation + treatment, list(sigma = ~ treatment))
#> <bayes_design>
#>   Type: Distributional regression model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 48
#>   fixed: irrigation, treatment

# Example 2
bayes_distributional(d, "yield", ~ irrigation * treatment, list(sigma = ~ irrigation + treatment))
#> <bayes_design>
#>   Type: Distributional regression model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 48
#>   fixed: irrigation, treatment

# Example 3
bayes_plan(bayes_distributional(d, "yield", ~ irrigation + treatment, list(sigma = ~ treatment)))
#> <bayes_plan>
#>   Design: Distributional regression model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
