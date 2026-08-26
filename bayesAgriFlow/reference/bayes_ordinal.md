# Specify an ordinal Bayesian model

Part of the bayesAgriFlow 1.0.0 complex-systems workflow. The scientific
data structure is stored separately from the computational backend.

## Usage

``` r
bayes_ordinal(data, response, fixed = NULL, group = NULL,
              family = c("cumulative", "cratio", "sratio", "acat"),
              link = c("logit", "probit", "cloglog", "cauchit"),
              threshold = c("flexible", "equidistant"))
```

## Arguments

- data:

  A data frame.

- response:

  Ordered factor or integer-coded ordered response.

- fixed:

  Fixed-effects specification.

- group:

  Optional grouping factors.

- family:

  Ordinal likelihood family.

- link:

  Ordinal link function.

- threshold:

  Threshold parameterization.

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
d <- bayes_teaching_data("ordinal")
bayes_ordinal(d, "severity", ~ treatment, group = "block")
#> <bayes_design>
#>   Type: Ordinal-response model
#>   Response: severity
#>   Family: Cumulative ordinal
#>   Observations: 144
#>   fixed: treatment
#>   group: block

# Example 2
bayes_ordinal(d, "severity", ~ treatment, family = "cumulative", link = "probit")
#> <bayes_design>
#>   Type: Ordinal-response model
#>   Response: severity
#>   Family: Cumulative ordinal
#>   Observations: 144
#>   fixed: treatment

# Example 3
bayes_plan(bayes_ordinal(d, "severity", ~ treatment, family = "cratio"))
#> <bayes_plan>
#>   Design: Ordinal-response model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
