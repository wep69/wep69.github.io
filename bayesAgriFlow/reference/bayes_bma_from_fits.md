# Compute BMA posterior model probabilities from fitted brms models

Repeat bridge sampling for compatible brms-backed models, retain
repeated log marginal likelihood estimates for numerical stability
assessment, and convert their mean log marginal likelihoods to posterior
model probabilities.

## Usage

``` r
bayes_bma_from_fits(fits, prior_weights = NULL, repetitions = 3L,
  seed = 20260824, ...)
```

## Arguments

- fits:

  Named list of two or more brms-backed `bayes_fit` objects.

- prior_weights:

  Optional prior model probabilities.

- repetitions:

  Number of independent bridge-sampling repetitions per model; at least
  two.

- seed:

  Base random seed.

- ...:

  Passed to
  [`brms::bridge_sampler()`](https://rdrr.io/pkg/bridgesampling/man/bridge_sampler.html).

## Value

A `bayes_bma` object containing posterior model-probability weights,
repeated log marginal likelihoods, stability summaries, and
bridge-sampling objects.

## Examples

``` r
# Example 1
if (FALSE) bayes_bma_from_fits(list(linear = fit1, quadratic = fit2))
# Example 2
if (FALSE) bayes_bma_from_fits(list(m1 = fit1, m2 = fit2), repetitions = 5)
# Example 3
if (FALSE) bayes_bma_from_fits(list(simple = fit1, complex = fit2),
  prior_weights = c(simple = .7, complex = .3))
```
