# Estimate first or second temporal derivatives

Estimate first or second temporal derivatives

## Usage

``` r
pheno_derivative(
  x,
  time = NULL,
  value = NULL,
  order = 1L,
  smooth = c("none", "loess", "spline")
)
```

## Arguments

- x:

  Numeric response, data frame, or `pheno_series`.

- time:

  Time vector or column name.

- value:

  Value-column name when needed.

- order:

  Derivative order, 1 or 2.

- smooth:

  Optional smoothing method (`"none"`, `"loess"`, or `"spline"`).

## Value

A tibble with time, processed value and derivative.

## References

Savitzky A, Golay MJE (1964). Analytical Chemistry 36:1627-1639.
[doi:10.1021/ac60214a047](https://doi.org/10.1021/ac60214a047) .

## Examples

``` r
d <- subset(pheno_data("growth_series"),plant_id=="P01"&trait=="leaf_area")
pheno_derivative(d,"day","value")
#> # A tibble: 5 × 5
#>    time value derivative order smoothing
#>   <dbl> <dbl>      <dbl> <int> <chr>    
#> 1     0  14.5       2.79     1 none     
#> 2     7  34.0       3.73     1 none     
#> 3    14  66.7       4.87     1 none     
#> 4    21 102.        3.56     1 none     
#> 5    28 117.        2.06     1 none     
pheno_derivative(d,"day","value",smooth="spline")
#> # A tibble: 5 × 5
#>    time value derivative order smoothing
#>   <dbl> <dbl>      <dbl> <int> <chr>    
#> 1     0  14.5       2.79     1 spline   
#> 2     7  34.0       3.73     1 spline   
#> 3    14  66.7       4.87     1 spline   
#> 4    21 102.        3.56     1 spline   
#> 5    28 117.        2.06     1 spline   
pheno_derivative(d,"day","value",order=2,smooth="loess")
#> Warning: span too small.   fewer data values than degrees of freedom.
#> Warning: pseudoinverse used at 0
#> Warning: neighborhood radius 14
#> Warning: reciprocal condition number  0
#> Warning: There are other near singularities as well. 49
#> Warning: span too small.   fewer data values than degrees of freedom.
#> Warning: pseudoinverse used at 0
#> Warning: neighborhood radius 14
#> Warning: reciprocal condition number  0
#> Warning: There are other near singularities as well. 49
#> # A tibble: 5 × 5
#>    time value derivative order smoothing
#>   <dbl> <dbl>      <dbl> <int> <chr>    
#> 1     0  14.5     0.134      2 loess    
#> 2     7  34.0     0.148      2 loess    
#> 3    14  66.7    -0.0116     2 loess    
#> 4    21 102.     -0.200      2 loess    
#> 5    28 117.     -0.215      2 loess    
```
