# Specify priors for bayesAgriFlow

Version 0.3.0 provides transparent regularizing defaults for posterior
estimation. These defaults are not universal scientific priors. Users
should inspect prior predictive behavior and replace them when domain
knowledge is available. Bayes-factor backends may use their own proper
default priors, which are reported separately.

## Usage

``` r
bayes_prior(strategy = c("regularizing", "skeptical", "weak", "custom"),
                        coefficient_scale = NULL, intercept_scale = NULL,
                        sigma_shape = 2, sigma_scale = 1,
                        brms_prior = NULL)
```

## Arguments

- strategy:

  \`"regularizing"\`, \`"skeptical"\`, \`"weak"\`, or \`"custom"\`.

- coefficient_scale:

  Scale multiplier for non-intercept Gaussian coefficients in the native
  engine, relative to residual scale.

- intercept_scale:

  Scale multiplier for the intercept.

- sigma_shape:

  Shape of the inverse-gamma prior on residual variance in the native
  engine.

- sigma_scale:

  Scale/rate parameter used for the inverse-gamma prior.

- brms_prior:

  Optional explicit \`brms\` prior object or vector.

## Value

A \`bayes_prior\` object.

## Examples

``` r
# Example 1: regularizing prior
bayes_prior()
#> <bayes_prior>
#>   Strategy: regularizing
#>   Coefficient scale: 2.5
#>   Intercept scale: 5
#>   Residual-variance prior: Inv-Gamma(shape=2, scale=1)
# Example 2: more skeptical coefficient prior
bayes_prior("skeptical")
#> <bayes_prior>
#>   Strategy: skeptical
#>   Coefficient scale: 1
#>   Intercept scale: 3
#>   Residual-variance prior: Inv-Gamma(shape=2, scale=1)
# Example 3: custom prior scales
bayes_prior("custom", coefficient_scale = 1.5, intercept_scale = 4,
            sigma_shape = 3, sigma_scale = 1)
#> <bayes_prior>
#>   Strategy: custom
#>   Coefficient scale: 1.5
#>   Intercept scale: 4
#>   Residual-variance prior: Inv-Gamma(shape=3, scale=1)
```
