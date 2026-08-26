# Summarize multivariate residual correlations

Part of the bayesAgriFlow 1.0.0 complex-systems workflow. The scientific
data structure is stored separately from the computational backend.

## Usage

``` r
bayes_multivariate_correlations(fit, level = 0.95)
```

## Arguments

- fit:

  A multivariate brms-backed fit.

- level:

  Credible interval mass.

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
if (FALSE) bayes_multivariate_correlations(multivariate_fit)

# Example 2
if (FALSE) bayes_multivariate_correlations(multivariate_fit, 0.90)

# Example 3
d <- bayes_teaching_data("multivariate")
bayes_plan(bayes_multivariate(d, c("biomass", "nitrogen_uptake"), ~ treatment))
#> <bayes_plan>
#>   Design: Multivariate Bayesian model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
