# Compute Bayes-factor evidence for supported fixed-design experiments

Bayes factors quantify relative model evidence under the priors of the
evidence model. They are not effect sizes and are not posterior model
probabilities. When called on a native posterior fit, the evidence model
is fitted separately with \`BayesFactor\`, and the object records that
its priors are not the same as the native estimation priors.

## Usage

``` r
bayes_evidence(fit, prior_odds = 1)
```

## Arguments

- fit:

  A \`bayes_fit\`.

- prior_odds:

  Prior odds for each reported model relative to the denominator model.
  Default 1.

## Value

A \`bayes_evidence\` object.

## Examples

``` r
# Example 1: evidence after a native CRD fit
if (requireNamespace("BayesFactor", quietly = TRUE)) {
  f <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
                 engine = "native", draws = 300)
  bayes_evidence(f)
}
#> <bayes_evidence>
#>      model       BF    error_pct   log_BF prior_odds posterior_odds
#>  treatment 69.23839 9.735353e-05 4.237556          1       69.23839
#>  posterior_probability_vs_denominator
#>                             0.9857628
#> 
#> Evidence was fitted separately with BayesFactor priors. Do not interpret it as arising from the native posterior-estimation prior.

# Example 2: direct BayesFactor engine
if (requireNamespace("BayesFactor", quietly = TRUE)) {
  f2 <- bayes_fit(bayes_rcbd(bayes_teaching_data("rcbd"), "yield",
                  "cultivar", "block"), engine = "bayesfactor")
  bayes_evidence(f2)
}
#> <bayes_evidence>
#>             model       BF  error_pct  log_BF prior_odds posterior_odds
#>  cultivar + block 12.25605 0.00707076 2.50602          1       12.25605
#>  posterior_probability_vs_denominator
#>                             0.9245628
#> 
#> Evidence uses the BayesFactor model priors.

# Example 3: non-unit prior odds are kept separate from the BF
if (requireNamespace("BayesFactor", quietly = TRUE)) {
  f3 <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
                  engine = "bayesfactor")
  bayes_evidence(f3, prior_odds = 0.5)
}
#> <bayes_evidence>
#>      model       BF    error_pct   log_BF prior_odds posterior_odds
#>  treatment 69.23839 9.735353e-05 4.237556        0.5        34.6192
#>  posterior_probability_vs_denominator
#>                             0.9719253
#> 
#> Evidence uses the BayesFactor model priors.
```
