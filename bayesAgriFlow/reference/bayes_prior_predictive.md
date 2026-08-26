# Generate prior predictive simulations

Prior predictive simulation is a model check, not an inferential result.
The native implementation uses the design matrix and response scale so
users can detect scientifically implausible prior predictions before
fitting.

## Usage

``` r
bayes_prior_predictive(design, prior = bayes_prior(), draws = 200,
                                   seed = 20260824)
```

## Arguments

- design:

  A \`bayes_design\`.

- prior:

  A \`bayes_prior\`.

- draws:

  Number of prior predictive draws.

- seed:

  Random seed.

## Value

A list with simulated summaries and optional draw matrix.

## Examples

``` r
# Example 1: CRD prior predictive check
d <- bayes_teaching_data("crd")
des <- bayes_crd(d, "yield", "treatment")
pp <- bayes_prior_predictive(des, bayes_prior(), draws = 50)
pp$summary
#>   statistic      value
#> 1   minimum -3.4589732
#> 2    median  6.4801013
#> 3   maximum 16.0224048
#> 4   mean_sd  0.9421453

# Example 2: skeptical prior
bayes_prior_predictive(des, bayes_prior("skeptical"), draws = 30)$summary
#>   statistic      value
#> 1   minimum  1.3653218
#> 2    median  6.6419935
#> 3   maximum 10.9387012
#> 4   mean_sd  0.6052249

# Example 3: RCBD prior predictive check
d2 <- bayes_teaching_data("rcbd")
des2 <- bayes_rcbd(d2, "yield", "cultivar", "block")
bayes_prior_predictive(des2, bayes_prior(), draws = 30)$summary
#>   statistic      value
#> 1   minimum -0.9119172
#> 2    median  7.7153959
#> 3   maximum 14.6360666
#> 4   mean_sd  1.2693598
```
