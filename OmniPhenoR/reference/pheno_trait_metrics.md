# Derived-trait agreement metrics

Derived-trait agreement metrics

## Usage

``` r
pheno_trait_metrics(truth, prediction)
```

## Arguments

- truth:

  True quantitative trait.

- prediction:

  Estimated trait.

## Value

One-row tibble with bias, MAE, RMSE, relative bias, correlation, and
CCC.

## Examples

``` r
pheno_trait_metrics(c(10,20,30),c(11,19,29))
#> # A tibble: 1 × 7
#>       n   bias   mae  rmse relative_bias correlation   ccc
#>   <int>  <dbl> <dbl> <dbl>         <dbl>       <dbl> <dbl>
#> 1     3 -0.333     1     1       -0.0167       0.998 0.992
pheno_trait_metrics(100,95)
#> # A tibble: 1 × 7
#>       n  bias   mae  rmse relative_bias correlation   ccc
#>   <int> <dbl> <dbl> <dbl>         <dbl>       <dbl> <dbl>
#> 1     1    -5     5     5         -0.05          NA    NA
pheno_trait_metrics(c(5,10,15,20), c(5.5,9.5,14,21))
#> # A tibble: 1 × 7
#>       n  bias   mae  rmse relative_bias correlation   ccc
#>   <int> <dbl> <dbl> <dbl>         <dbl>       <dbl> <dbl>
#> 1     4     0  0.75 0.791             0       0.991 0.990
```
