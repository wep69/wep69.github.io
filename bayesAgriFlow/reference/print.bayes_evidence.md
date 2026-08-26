# Print a bayes_evidence object

Compact console display for \`bayes_evidence\` objects: posterior
evidence quantities for treatment comparisons.

## Usage

``` r
# S3 method for class 'bayes_evidence'
print(x, ...)
```

## Arguments

- x:

  An object of class \`bayes_evidence\`: Bayesian evidence summary.

- ...:

  Additional arguments passed to or from other methods.

## Value

The object \`x\`, invisibly.

## Examples

``` r
des <- bayes_crd(bayes_teaching_data("crd"), "yield", "treatment")
fit <- bayes_fit(des, engine = "native", draws = 1000)
ev <- bayes_evidence(fit)
ev
#> <bayes_evidence>
#>      model       BF    error_pct   log_BF prior_odds posterior_odds
#>  treatment 69.23839 9.735353e-05 4.237556          1       69.23839
#>  posterior_probability_vs_denominator
#>                             0.9857628
#> 
#> Evidence was fitted separately with BayesFactor priors. Do not interpret it as arising from the native posterior-estimation prior.
```
