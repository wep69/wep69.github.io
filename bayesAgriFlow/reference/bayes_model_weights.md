# Compute predictive model weights

Compute predictive stacking, pseudo-BMA+, or pseudo-BMA weights from
compatible brms-backed Bayesian models using PSIS-LOO predictive
information.

## Usage

``` r
bayes_model_weights(fits,
                    method = c("stacking", "pseudobma+", "pseudobma"),
                    cores = 1, bb_n = 1000, seed = 20260824)
```

## Arguments

- fits:

  Named list of two or more compatible `bayes_fit` objects.

- method:

  Predictive weighting method.

- cores:

  Number of CPU cores passed to
  [`loo::loo_model_weights()`](https://mc-stan.org/loo/reference/loo_model_weights.html).

- bb_n:

  Number of Bayesian-bootstrap replicates for pseudo-BMA+.

- seed:

  Random seed used by pseudo-BMA+ weighting.

## Value

A `bayes_model_weights` object. Its weights are predictive combination
weights, not posterior model probabilities.

## Examples

``` r
# Example 1
if (FALSE) bayes_model_weights(list(linear = fit1, quadratic = fit2))
# Example 2
if (FALSE) bayes_model_weights(list(m1 = fit1, m2 = fit2), method = "pseudobma+")
# Example 3
if (FALSE) bayes_model_weights(list(m1 = fit1, m2 = fit2), method = "pseudobma", cores = 4)
```
