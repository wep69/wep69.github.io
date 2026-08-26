# Compare Bayesian models with a Bayes factor

For two \`brms\` fits this calls the \`brms\` bridge-sampling interface.
Proper priors and saved parameters are required. For fixed-design
BayesFactor fits, leaving \`fit0\` empty returns the model-evidence
table.

## Usage

``` r
bayes_bf(fit1, fit0 = NULL, prior_odds = 1, ...)
```

## Arguments

- fit1:

  First \`bayes_fit\`.

- fit0:

  Optional second \`bayes_fit\`.

- prior_odds:

  Prior odds for \`fit1\` versus \`fit0\` when reported.

- ...:

  Passed to \`brms::bayes_factor()\` for two brms fits.

## Value

A data frame or backend Bayes-factor object.

## Examples

``` r
# Example 1: model-space BFs in a CRD
if (requireNamespace("BayesFactor", quietly = TRUE)) {
  f <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
                 engine = "bayesfactor")
  bayes_bf(f)
}
#>       model       BF    error_pct   log_BF prior_odds posterior_odds
#> 1 treatment 69.23839 9.735353e-05 4.237556          1       69.23839
#>   posterior_probability_vs_denominator
#> 1                            0.9857628

# Example 2: prior odds are reported separately
if (requireNamespace("BayesFactor", quietly = TRUE)) {
  f <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
                 engine = "bayesfactor")
  bayes_bf(f, prior_odds = 2)
}
#>       model       BF    error_pct   log_BF prior_odds posterior_odds
#> 1 treatment 69.23839 9.735353e-05 4.237556          2       138.4768
#>   posterior_probability_vs_denominator
#> 1                            0.9928303

# Example 3: two brms fits can be bridge-sampled after local fitting
if (FALSE) {
  bf <- bayes_bf(full_fit, reduced_fit)
}
```
