# Posterior predictions on the response scale

Provides a common prediction interface for the native Gaussian engine
and brms-backed models. For generalized models, \`type = "mean"\`
returns expected responses on the response scale rather than link-scale
coefficients.

## Usage

``` r
bayes_predict(fit, newdata = NULL, type = c("mean", "predictive"),
                          level = 0.95, re_formula = NA, ndraws = NULL,
                          seed = 20260824, response = NULL)
```

## Arguments

- fit:

  A posterior-estimation \`bayes_fit\`.

- newdata:

  Optional prediction data. Defaults to the fitted data.

- type:

  \`"mean"\` for posterior expected responses or \`"predictive"\` for
  posterior predictive observations.

- level:

  Credible interval mass.

- re_formula:

  Group-level effects specification passed to brms. The default \`NA\`
  returns population-level predictions.

- ndraws:

  Optional number of draws retained from a brms posterior.

- seed:

  Random seed for native posterior predictive simulation.

- response:

  Optional response name for multivariate brms models.

## Value

A list with a row-wise summary and the prediction-draw matrix.

## Examples

``` r
# Example 1: fitted CRD posterior means
f <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"), draws = 500)
head(bayes_predict(f)$summary)
#>   row     mean   median    lower    upper
#> 1   1 6.051675 6.054663 5.761319 6.333498
#> 2   2 6.051675 6.054663 5.761319 6.333498
#> 3   3 6.051675 6.054663 5.761319 6.333498
#> 4   4 6.051675 6.054663 5.761319 6.333498
#> 5   5 6.051675 6.054663 5.761319 6.333498
#> 6   6 6.051675 6.054663 5.761319 6.333498

# Example 2: predictions at nitrogen values from a polynomial model
d <- bayes_teaching_data("dose")
fp <- bayes_fit(bayes_polynomial(d, "yield", "nitrogen", 2), draws = 500)
bayes_predict(fp, data.frame(nitrogen = c(0, 100, 200)))$summary
#>   row     mean   median    lower    upper
#> 1   1 5.350053 5.340461 4.835618 5.857170
#> 2   2 9.303358 9.296514 8.994015 9.603019
#> 3   3 9.437997 9.446779 8.915972 9.916274

# Example 3: response-scale predictions from a local GLMM fit
if (FALSE) bayes_predict(glmm_fit, type = "mean")
```
