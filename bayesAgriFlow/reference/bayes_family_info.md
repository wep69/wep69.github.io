# Describe response families available in version 0.3.0

Describe response families available in version 0.3.0

## Usage

``` r
bayes_family_info(family = NULL)
```

## Arguments

- family:

  Optional family name used to filter the table.

## Value

A data frame describing response support and default links.

## Examples

``` r
# Example 1: all families
bayes_family_info()
#>        family default_link                           response         engine
#> 1    gaussian     identity                    continuous real native or brms
#> 2     student     identity continuous real with heavier tails           brms
#> 3   bernoulli        logit                         binary 0/1           brms
#> 4    binomial        logit            successes out of trials           brms
#> 5     poisson          log                non-negative counts           brms
#> 6 negbinomial          log  overdispersed non-negative counts           brms
#> 7       gamma          log                positive continuous           brms
#> 8   lognormal     identity                positive continuous           brms
#> 9        beta        logit     continuous proportion in (0,1)           brms
# Example 2: count families
subset(bayes_family_info(), family %in% c("poisson", "negbinomial"))
#>        family default_link                          response engine
#> 5     poisson          log               non-negative counts   brms
#> 6 negbinomial          log overdispersed non-negative counts   brms
# Example 3: inspect the binomial route
bayes_family_info("binomial")
#>     family default_link                response engine
#> 1 binomial        logit successes out of trials   brms
```
