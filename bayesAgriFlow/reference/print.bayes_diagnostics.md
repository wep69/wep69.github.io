# Print a bayes_diagnostics object

Compact console display for \`bayes_diagnostics\` objects: convergence
and sampler-health summaries for Stan-backed fits.

## Usage

``` r
# S3 method for class 'bayes_diagnostics'
print(x, ...)
```

## Arguments

- x:

  An object of class \`bayes_diagnostics\`: MCMC diagnostic summary.

- ...:

  Additional arguments passed to or from other methods.

## Value

The object \`x\`, invisibly.

## Examples

``` r
des <- bayes_crd(bayes_teaching_data("crd"), "yield", "treatment")
fit <- bayes_fit(des, engine = "native", draws = 1000)
dg <- bayes_diagnose(fit)
dg
#> <bayes_diagnostics>
#>   Engine: native
#>   Status: PASS
#>   The native engine samples directly from the conjugate posterior. R-hat, ESS, divergences, tree depth, and E-BFMI are not MCMC diagnostics for this engine.
```
