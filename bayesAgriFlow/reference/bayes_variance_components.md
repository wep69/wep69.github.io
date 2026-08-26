# Summarize posterior variance components and dependence parameters

Summarize posterior variance components and dependence parameters

## Usage

``` r
bayes_variance_components(fit, level = 0.95)
```

## Arguments

- fit:

  A \`bayes_fit\` from a posterior-estimation engine.

- level:

  Credible interval mass.

## Value

A data frame for group SDs, correlations, residual sigma, and AR
parameters when they exist.

## Examples

``` r
# Example 1: function is intended for a local brms mixed fit
if (FALSE) bayes_variance_components(mixed_fit)
# Example 2: request 90 percent intervals
if (FALSE) bayes_variance_components(mixed_fit, level = 0.90)
# Example 3: repeated-measures dependence parameters
if (FALSE) bayes_variance_components(repeated_fit)
```
