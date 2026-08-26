# Specify a censored-response Bayesian model

Part of the bayesAgriFlow 1.0.0 complex-systems workflow. The scientific
data structure is stored separately from the computational backend.

## Usage

``` r
bayes_censored(data, response, fixed = NULL, censor,
               censor_upper = NULL, group = NULL,
               family = c("gaussian", "student", "lognormal", "gamma",
                          "weibull"))
```

## Arguments

- data:

  A data frame.

- response:

  Response or censoring boundary.

- fixed:

  Fixed-effects specification.

- censor:

  Censoring indicator column.

- censor_upper:

  Upper boundary for interval-censored observations.

- group:

  Optional grouping factors.

- family:

  Continuous response family.

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
d <- bayes_teaching_data("censored")
bayes_censored(d, "residue", ~ treatment, "censor", group = "block", family = "lognormal")
#> <bayes_design>
#>   Type: Censored-response model
#>   Response: residue
#>   Family: Lognormal
#>   Observations: 108
#>   fixed: treatment
#>   group: block
#>   censor: censor

# Example 2
bayes_censored(d, "residue", ~ treatment, "censor", family = "gaussian")
#> <bayes_design>
#>   Type: Censored-response model
#>   Response: residue
#>   Family: Gaussian
#>   Observations: 108
#>   fixed: treatment
#>   censor: censor

# Example 3
bayes_censoring_summary(bayes_censored(d, "residue", ~ treatment, "censor", family = "lognormal"))
#>   censoring  n proportion
#> 1      left 35  0.3240741
#> 2      none 73  0.6759259
```
