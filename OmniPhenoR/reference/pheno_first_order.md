# First-order image texture statistics

Summarizes the marginal gray-level distribution independently of spatial
adjacency. These features complement, rather than replace, second-order
or multiscale texture descriptors.

## Usage

``` r
pheno_first_order(x, mask = NULL, bins = 64L)
```

## Arguments

- x:

  Numeric gray matrix or RGB array/list.

- mask:

  Optional logical matrix selecting a region of interest.

- bins:

  Number of histogram bins used for entropy and uniformity.

## Value

A one-row tibble.

## Examples

``` r
pheno_first_order(pheno_data("leaf_gray"))
#> # A tibble: 1 × 18
#>       n   min   q05   q25 median   q75   q95   max  mean     sd variance    cv
#>   <int> <dbl> <dbl> <dbl>  <dbl> <dbl> <dbl> <dbl> <dbl>  <dbl>    <dbl> <dbl>
#> 1 12288 0.322 0.422 0.670  0.670 0.670 0.670 0.670 0.617 0.0980  0.00960 0.159
#> # ℹ 6 more variables: skewness <dbl>, kurtosis_excess <dbl>, entropy <dbl>,
#> #   uniformity <dbl>, iqr <dbl>, mad <dbl>
pheno_first_order(pheno_data("leaf_gray"), mask=pheno_data("leaf_mask"), bins=32)
#> # A tibble: 1 × 18
#>       n   min   q05   q25 median   q75   q95   max  mean     sd variance    cv
#>   <int> <dbl> <dbl> <dbl>  <dbl> <dbl> <dbl> <dbl> <dbl>  <dbl>    <dbl> <dbl>
#> 1  2872 0.322 0.322 0.428  0.456 0.480 0.508 0.539 0.446 0.0521  0.00271 0.117
#> # ℹ 6 more variables: skewness <dbl>, kurtosis_excess <dbl>, entropy <dbl>,
#> #   uniformity <dbl>, iqr <dbl>, mad <dbl>
pheno_first_order(matrix(seq(0,1,length.out=100),10), bins=10)
#> # A tibble: 1 × 18
#>       n   min   q05   q25 median   q75   q95   max  mean    sd variance    cv
#>   <int> <dbl> <dbl> <dbl>  <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>    <dbl> <dbl>
#> 1   100     0  0.05  0.25    0.5  0.75  0.95     1   0.5 0.293   0.0859 0.586
#> # ℹ 6 more variables: skewness <dbl>, kurtosis_excess <dbl>, entropy <dbl>,
#> #   uniformity <dbl>, iqr <dbl>, mad <dbl>
```
