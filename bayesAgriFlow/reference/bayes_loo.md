# Compute PSIS-LOO for a brms-backed model

Compute PSIS-LOO for a brms-backed model

## Usage

``` r
bayes_loo(fit, ...)
```

## Arguments

- fit:

  A brms-backed \`bayes_fit\`.

- ...:

  Passed to \`brms::loo()\`.

## Value

A \`loo\` object.

## Examples

``` r
# Example 1: compute LOO after a local mixed-model fit
if (FALSE) bayes_loo(mixed_fit)
# Example 2: save pointwise information
if (FALSE) bayes_loo(mixed_fit, pointwise = TRUE)
# Example 3: inspect Pareto-k diagnostics
if (FALSE) loo::pareto_k_table(bayes_loo(mixed_fit))
```
