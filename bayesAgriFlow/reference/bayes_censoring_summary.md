# Summarize censoring categories

Part of the bayesAgriFlow 1.0.0 complex-systems workflow. The scientific
data structure is stored separately from the computational backend.

## Usage

``` r
bayes_censoring_summary(x)
```

## Arguments

- x:

  A censored design or fitted model.

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
des <- bayes_censored(d, "residue", ~ treatment, "censor", family = "lognormal")
bayes_censoring_summary(des)
#>   censoring  n proportion
#> 1      left 35  0.3240741
#> 2      none 73  0.6759259

# Example 2
bayes_censoring_summary(bayes_censored(d, "residue", ~ treatment,
                        "censor", group = "block", family = "lognormal"))
#>   censoring  n proportion
#> 1      left 35  0.3240741
#> 2      none 73  0.6759259

# Example 3
if (FALSE) bayes_censoring_summary(censored_fit)
```
