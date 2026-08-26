# Print a Bayesian-bootstrap summary

Print the posterior functional, number of draws, and group summaries.

## Usage

``` r
# S3 method for class 'bayes_bootstrap'
print(x, ...)
```

## Arguments

- x:

  A `bayes_bootstrap` object.

- ...:

  Unused additional arguments.

## Value

The input object, invisibly.

## Examples

``` r
# Example 1
bb <- bayes_bootstrap(bayes_teaching_data("crd"), "yield", draws = 200)
print(bb)
#> <bayes_bootstrap>
#>   Statistic: mean
#>   Draws: 200
#>    group     mean   median         sd    lower    upper
#>  overall 6.471257 6.470768 0.08796705 6.322349 6.645669
# Example 2
bb2 <- bayes_bootstrap(bayes_teaching_data("crd"), "yield", "treatment", draws = 200)
print(bb2)
#> <bayes_bootstrap>
#>   Statistic: mean
#>   Draws: 200
#>    group     mean   median         sd    lower    upper
#>     BioA 6.236441 6.230176 0.12091816 6.018500 6.503943
#>     BioB 6.681973 6.680924 0.08446556 6.526767 6.850794
#>     BioC 6.955481 6.954035 0.18275173 6.564386 7.339413
#>  Control 6.052974 6.049869 0.10340001 5.865061 6.272195
# Example 3
invisible(print(bb2))
#> <bayes_bootstrap>
#>   Statistic: mean
#>   Draws: 200
#>    group     mean   median         sd    lower    upper
#>     BioA 6.236441 6.230176 0.12091816 6.018500 6.503943
#>     BioB 6.681973 6.680924 0.08446556 6.526767 6.850794
#>     BioC 6.955481 6.954035 0.18275173 6.564386 7.339413
#>  Control 6.052974 6.049869 0.10340001 5.865061 6.272195
```
