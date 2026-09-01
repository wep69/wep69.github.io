# Summarize a Monte Carlo or scenario ensemble

Summarize a Monte Carlo or scenario ensemble

## Usage

``` r
uncertainty_summary(x, probs = c(0.05, 0.5, 0.95))
```

## Arguments

- x:

  An \`agri_uncertainty_ensemble\`.

- probs:

  Quantile probabilities. Defaults to P05, P50, and P95.

## Value

An \`agri_uncertainty_summary\` with mean, weighted SD, quantiles, and
number of available members for each spatial/numeric unit.
