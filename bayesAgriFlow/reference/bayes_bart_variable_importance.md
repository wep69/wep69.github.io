# Variable inclusion information for a PyMC-BART fit

Extract PyMC-BART variable inclusion information from an optional BART
fit.

## Usage

``` r
bayes_bart_variable_importance(fit)
```

## Arguments

- fit:

  A PyMC-BART bayes_fit object.

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
if (FALSE) bayes_bart_variable_importance(bart_fit)

# Example 2
if (FALSE) head(bayes_bart_variable_importance(bart_fit))

# Example 3
if (FALSE) {
  vi <- bayes_bart_variable_importance(bart_fit)
  print(vi)
}
```
