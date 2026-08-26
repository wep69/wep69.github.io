# Summarize a prior specification

Summarize a prior specification

## Usage

``` r
bayes_prior_summary(prior = bayes_prior())
```

## Arguments

- prior:

  A \`bayes_prior\`.

## Value

A one-row data frame.

## Examples

``` r
# Example 1
bayes_prior_summary(bayes_prior())
#>       strategy coefficient_scale intercept_scale sigma_shape sigma_scale
#> 1 regularizing               2.5               5           2           1
#>   explicit_brms_prior
#> 1               FALSE
# Example 2
bayes_prior_summary(bayes_prior("skeptical"))
#>    strategy coefficient_scale intercept_scale sigma_shape sigma_scale
#> 1 skeptical                 1               3           2           1
#>   explicit_brms_prior
#> 1               FALSE
# Example 3
bayes_prior_summary(bayes_prior("custom", coefficient_scale = 1.5))
#>   strategy coefficient_scale intercept_scale sigma_shape sigma_scale
#> 1   custom               1.5               5           2           1
#>   explicit_brms_prior
#> 1               FALSE
```
