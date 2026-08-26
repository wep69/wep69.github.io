# Compute formal Bayesian model-averaging weights

Convert model log marginal likelihoods and prior model probabilities
into posterior model probabilities over the supplied candidate-model
set.

## Usage

``` r
bayes_bma_weights(log_marginal, prior_weights = NULL)
```

## Arguments

- log_marginal:

  Named numeric vector of model log marginal likelihoods.

- prior_weights:

  Optional prior model probabilities. Equal probabilities are used when
  omitted.

## Value

A `bayes_model_weights` object whose weights are posterior model
probabilities conditional on the supplied model set and priors.

## Examples

``` r
# Example 1
bayes_bma_weights(c(linear = -120.4, quadratic = -116.1, cubic = -117.8))
#> bayesAgriFlow model weights
#> Method: marginal-likelihood BMA
#>       model     weight
#> 1    linear 0.01134256
#> 2 quadratic 0.83594421
#> 3     cubic 0.15271323
# Example 2
bayes_bma_weights(c(m1 = -20, m2 = -19), prior_weights = c(m1 = .8, m2 = .2))
#> bayesAgriFlow model weights
#> Method: marginal-likelihood BMA
#>   model    weight
#> 1    m1 0.5953903
#> 2    m2 0.4046097
# Example 3
bayes_bma_weights(c(simple = -50, complex = -48))$weights
#>    simple   complex 
#> 0.1192029 0.8807971 
```
