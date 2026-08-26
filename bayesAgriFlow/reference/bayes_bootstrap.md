# Bayesian bootstrap for means or medians

Draw Rubin Bayesian-bootstrap posterior weights and summarize overall or
group-specific means or medians.

## Usage

``` r
bayes_bootstrap(data, response, group = NULL,
                statistic = c("mean", "median"), draws = 4000L,
                seed = 20260824, level = 0.95)
```

## Arguments

- data:

  A data frame.

- response:

  Response column name.

- group:

  Optional grouping factor.

- statistic:

  Posterior functional, mean or median.

- draws:

  Number of posterior weight draws.

- seed:

  Random seed.

- level:

  Credible interval mass.

## Value

An object or summary described in the function documentation.

## Details

This interface is part of bayesAgriFlow 1.0.0. Optional brms, Stan, and
Python backends are never installed or invoked silently. Scientific
interpretation should be based on posterior uncertainty, model
diagnostics, and the stated estimand rather than on a single index.

## Examples

``` r
# Example 1
bayes_bootstrap(bayes_teaching_data("crd"), "yield", draws = 500)
#> <bayes_bootstrap>
#>   Statistic: mean
#>   Draws: 500
#>    group    mean   median        sd  lower    upper
#>  overall 6.48504 6.479539 0.0864509 6.3261 6.656665

# Example 2
bb <- bayes_bootstrap(bayes_teaching_data("crd"), "yield", "treatment", draws = 500)
bb$summary
#>     group     mean   median        sd    lower    upper
#> 1    BioA 6.237614 6.231098 0.1131034 6.026834 6.477248
#> 2    BioB 6.680563 6.674689 0.0849634 6.535577 6.848194
#> 3    BioC 6.968158 6.981697 0.1858164 6.587866 7.306022
#> 4 Control 6.052183 6.046134 0.1050820 5.883856 6.287852

# Example 3
bayes_bootstrap(bayes_teaching_data("crd"), "yield", "treatment", statistic = "median", draws = 500)
#> <bayes_bootstrap>
#>   Statistic: median
#>   Draws: 500
#>    group     mean   median        sd    lower    upper
#>     BioA 6.207391 6.124542 0.1653371 5.858721 6.600682
#>     BioB 6.637995 6.733959 0.1653287 6.447090 6.982219
#>     BioC 6.986414 7.031929 0.3379011 6.494981 7.647230
#>  Control 5.969099 5.931464 0.1618525 5.799554 6.388969
```
