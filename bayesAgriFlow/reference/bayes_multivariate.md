# Specify a multivariate Bayesian model

Part of the bayesAgriFlow 1.0.0 complex-systems workflow. The scientific
data structure is stored separately from the computational backend.

## Usage

``` r
bayes_multivariate(data, responses, fixed = NULL, group = NULL,
                   families = "gaussian", rescor = TRUE)
```

## Arguments

- data:

  A data frame.

- responses:

  Character vector of response columns.

- fixed:

  Shared fixed-effects specification.

- group:

  Optional grouping factors.

- families:

  One response family or one per response.

- rescor:

  Estimate residual response correlations when supported.

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
d <- bayes_teaching_data("multivariate")
bayes_multivariate(d, c("biomass", "nitrogen_uptake"), ~ treatment, group = "block")
#> <bayes_design>
#>   Type: Multivariate Bayesian model
#>   Response: biomassnitrogen_uptake
#>   Family: Multivariate
#>   Observations: 54
#>   fixed: treatment
#>   group: block

# Example 2
bayes_multivariate(d, c("biomass", "nitrogen_uptake"), ~ treatment, rescor = FALSE)
#> <bayes_design>
#>   Type: Multivariate Bayesian model
#>   Response: biomassnitrogen_uptake
#>   Family: Multivariate
#>   Observations: 54
#>   fixed: treatment

# Example 3
bayes_plan(bayes_multivariate(d, c("biomass", "nitrogen_uptake"), ~ treatment))
#> <bayes_plan>
#>   Design: Multivariate Bayesian model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
