# Smooth a longitudinal phenotype trajectory

Smooth a longitudinal phenotype trajectory

## Usage

``` r
pheno_smooth(
  x,
  time = NULL,
  value = NULL,
  method = c("loess", "spline", "moving_average"),
  span = 0.6,
  window = 3L
)
```

## Arguments

- x:

  Numeric response, data frame, or `pheno_series`.

- time:

  Numeric time vector or time-column name.

- value:

  Value-column name when `x` is a data frame/series.

- method:

  `"loess"`, `"spline"`, or `"moving_average"`.

- span:

  LOESS span or moving-average proportion.

- window:

  Moving-average window size.

## Value

A data frame with raw and smoothed values.

## Examples

``` r
d <- subset(pheno_data("growth_series"), plant_id == "P01" & trait == "leaf_area")
pheno_smooth(d, "day", "value", method = "loess")
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
#> # A tibble: 5 × 4
#>    time   raw smoothed method
#>   <dbl> <dbl>    <dbl> <chr> 
#> 1     0  14.5     14.5 loess 
#> 2     7  34.0     34.0 loess 
#> 3    14  66.7     66.7 loess 
#> 4    21 102.     102.  loess 
#> 5    28 117.     117.  loess 
pheno_smooth(d, "day", "value", method = "spline")
#> # A tibble: 5 × 4
#>    time   raw smoothed method
#>   <dbl> <dbl>    <dbl> <chr> 
#> 1     0  14.5     14.5 spline
#> 2     7  34.0     34.0 spline
#> 3    14  66.7     66.7 spline
#> 4    21 102.     102.  spline
#> 5    28 117.     117.  spline
pheno_smooth(d, "day", "value", method = "moving_average", window = 3)
#> # A tibble: 5 × 4
#>    time   raw smoothed method        
#>   <dbl> <dbl>    <dbl> <chr>         
#> 1     0  14.5     NA   moving_average
#> 2     7  34.0     38.4 moving_average
#> 3    14  66.7     67.6 moving_average
#> 4    21 102.      95.1 moving_average
#> 5    28 117.      NA   moving_average
```
