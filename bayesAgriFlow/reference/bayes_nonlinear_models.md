# Available nonlinear response templates

List the nonlinear response templates implemented in version 1.0.0.

## Usage

``` r
bayes_nonlinear_models(model = NULL)
```

## Arguments

- model:

  Optional character template name; when \`NULL\`, all templates are
  returned.

## Value

An object or summary described in the function documentation.

## Details

This interface is part of bayesAgriFlow 1.0.0. Optional brms, Stan, and
Python backends are never installed or invoked silently. Scientific
interpretation should be based on posterior uncertainty, model
diagnostics, and the stated estimand rather than on a single index.

## Examples

``` r
# Example 1
bayes_nonlinear_models()
#>              model                     parameters
#> 1 michaelis_menten                     asym, half
#> 2     mitscherlich              lower, asym, rate
#> 3        logistic4      lower, upper, ed50, scale
#> 4         gompertz lower, upper, rate, inflection
#>                                                       agronomic_use
#> 1              Saturating response to a positive quantitative input
#> 2                     Diminishing-return response with an asymptote
#> 3 Sigmoidal dose or growth response with lower and upper asymptotes
#> 4                      Asymmetric sigmoidal growth or dose response

# Example 2
names(bayes_nonlinear_models())
#> [1] "model"         "parameters"    "agronomic_use"

# Example 3
bayes_nonlinear_models()[["mitscherlich"]]
#> NULL
```
