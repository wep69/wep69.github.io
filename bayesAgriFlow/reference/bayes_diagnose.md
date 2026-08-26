# Diagnose posterior computation

For Stan fits, reports R-hat, bulk/tail ESS, MCSE, divergences,
tree-depth saturation, and an E-BFMI summary when the energy diagnostic
is available. Native conjugate draws are independent Monte Carlo draws
and therefore do not require MCMC convergence diagnostics.

## Usage

``` r
bayes_diagnose(fit, rhat_limit = 1.01, ess_limit = 400)
```

## Arguments

- fit:

  A \`bayes_fit\`.

- rhat_limit:

  Diagnostic warning threshold.

- ess_limit:

  Minimum bulk/tail ESS warning threshold.

## Value

A \`bayes_diagnostics\` object.

## Examples

``` r
# Example 1: native CRD diagnostics
f <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
               draws = 500)
bayes_diagnose(f)
#> <bayes_diagnostics>
#>   Engine: native
#>   Status: PASS
#>   The native engine samples directly from the conjugate posterior. R-hat, ESS, divergences, tree depth, and E-BFMI are not MCMC diagnostics for this engine.

# Example 2: native RCBD diagnostics
f2 <- bayes_fit(bayes_rcbd(bayes_teaching_data("rcbd"), "yield",
                "cultivar", "block"), draws = 500)
bayes_diagnose(f2)
#> <bayes_diagnostics>
#>   Engine: native
#>   Status: PASS
#>   The native engine samples directly from the conjugate posterior. R-hat, ESS, divergences, tree depth, and E-BFMI are not MCMC diagnostics for this engine.

# Example 3: Stan diagnostics are available after a local brms fit
if (FALSE) bayes_diagnose(fit_from_brms)
```
