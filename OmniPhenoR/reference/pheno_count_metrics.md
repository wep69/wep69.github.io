# Counting error metrics

Counting error metrics

## Usage

``` r
pheno_count_metrics(truth, prediction)
```

## Arguments

- truth:

  Numeric true counts.

- prediction:

  Numeric predicted counts.

## Value

One-row tibble.

## Examples

``` r
pheno_count_metrics(c(10,12,9),c(11,11,8))
#> # A tibble: 1 × 6
#>       n   bias   mae  rmse relative_error correlation
#>   <int>  <dbl> <dbl> <dbl>          <dbl>       <dbl>
#> 1     3 -0.333     1     1         0.0981       0.756
pheno_count_metrics(20,18)
#> # A tibble: 1 × 6
#>       n  bias   mae  rmse relative_error correlation
#>   <int> <dbl> <dbl> <dbl>          <dbl>       <dbl>
#> 1     1    -2     2     2            0.1          NA
pheno_count_metrics(c(0,5,10), c(1,5,9))
#> # A tibble: 1 × 6
#>       n  bias   mae  rmse relative_error correlation
#>   <int> <dbl> <dbl> <dbl>          <dbl>       <dbl>
#> 1     3     0 0.667 0.816           0.05           1
```
