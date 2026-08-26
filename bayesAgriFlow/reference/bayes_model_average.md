# Average posterior predictive distributions across Bayesian models

Combine compatible model-specific posterior predictions using supplied
weights or predictive PSIS-LOO weights. Mixture draws preserve
between-model uncertainty.

## Usage

``` r
bayes_model_average(fits, newdata = NULL, weights = NULL,
                    method = c("stacking", "pseudobma+", "pseudobma"),
                    type = c("mean", "predictive"), level = 0.95,
                    ndraws = NULL, seed = 20260824, ...)
```

## Arguments

- fits:

  Named list of compatible `bayes_fit` objects.

- newdata:

  Optional common prediction data.

- weights:

  Optional numeric weights or a `bayes_model_weights` object.

- method:

  Predictive weighting method when `weights` is omitted.

- type:

  Expected-response or posterior-predictive averaging.

- level:

  Credible interval mass.

- ndraws:

  Optional maximum number of mixture draws.

- seed:

  Random seed for mixture-component sampling.

- ...:

  Passed to
  [`bayes_model_weights()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_model_weights.md)
  when weights are computed internally.

## Value

A `bayes_model_average` object containing summaries, mixture draws,
weighted-mean draws, model predictions, and weights.

## Examples

``` r
# Example 1
if (FALSE) bayes_model_average(list(linear = fit1, quadratic = fit2))
# Example 2
if (FALSE) {
  w <- bayes_bma_weights(c(linear = -120, quadratic = -116))
  bayes_model_average(list(linear = fit1, quadratic = fit2), weights = w)
}
# Example 3
if (FALSE) bayes_model_average(list(m1=fit1,m2=fit2), type="predictive", ndraws=1000)
```
