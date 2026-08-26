# Specify a finite Bayesian mixture model

Part of the bayesAgriFlow 1.0.0 complex-systems workflow. The scientific
data structure is stored separately from the computational backend.

## Usage

``` r
bayes_mixture(data, response, fixed = NULL, group = NULL,
              components = c("gaussian", "gaussian"), order = NULL)
```

## Arguments

- data:

  A data frame.

- response:

  Continuous response.

- fixed:

  Common fixed-effects specification.

- group:

  Optional grouping factors.

- components:

  Mixture component families.

- order:

  Component-identification ordering rule.

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
d <- bayes_teaching_data("mixture")
bayes_mixture(d, "seed_weight", ~ treatment)
#> <bayes_design>
#>   Type: Finite mixture model
#>   Response: seed_weight
#>   Family: Finite mixture
#>   Observations: 200
#>   fixed: treatment

# Example 2
bayes_mixture(d, "seed_weight", ~ treatment, components = rep("gaussian", 3))
#> <bayes_design>
#>   Type: Finite mixture model
#>   Response: seed_weight
#>   Family: Finite mixture
#>   Observations: 200
#>   fixed: treatment

# Example 3
bayes_plan(bayes_mixture(d, "seed_weight", ~ treatment))
#> <bayes_plan>
#>   Design: Finite mixture model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
