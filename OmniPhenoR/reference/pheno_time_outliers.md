# Flag longitudinal outliers without deleting them

Flag longitudinal outliers without deleting them

## Usage

``` r
pheno_time_outliers(
  x,
  time = NULL,
  value = NULL,
  method = c("local_mad", "loess_residual"),
  threshold = 3.5,
  window = 2L
)
```

## Arguments

- x:

  Numeric response, data frame, or `pheno_series`.

- time:

  Time vector or column name.

- value:

  Value-column name when needed.

- method:

  `"local_mad"` or `"loess_residual"`.

- threshold:

  Standardized threshold.

- window:

  Local window radius for the MAD method.

## Value

A tibble with score and logical outlier flag.

## Examples

``` r
d <- subset(pheno_data("growth_series"), plant_id == "P01" & trait == "leaf_area")
pheno_time_outliers(d, "day", "value")
#> # A tibble: 5 × 5
#>    time value score outlier method   
#>   <dbl> <dbl> <dbl> <lgl>   <chr>    
#> 1     0  14.5 1     FALSE   local_mad
#> 2     7  34.0 0.626 FALSE   local_mad
#> 3    14  66.7 0     FALSE   local_mad
#> 4    21 102.  0.711 FALSE   local_mad
#> 5    28 117.  1     FALSE   local_mad
pheno_time_outliers(d, "day", "value", method = "loess_residual")
#> Warning: span too small.   fewer data values than degrees of freedom.
#> Warning: pseudoinverse used at 0
#> Warning: neighborhood radius 14
#> Warning: reciprocal condition number  0
#> Warning: There are other near singularities as well. 49
#> # A tibble: 5 × 5
#>    time value   score outlier method        
#>   <dbl> <dbl>   <dbl> <lgl>   <chr>         
#> 1     0  14.5 0.00178 FALSE   loess_residual
#> 2     7  34.0 0       FALSE   loess_residual
#> 3    14  66.7 0       FALSE   loess_residual
#> 4    21 102.  0       FALSE   loess_residual
#> 5    28 117.  0       FALSE   loess_residual
pheno_time_outliers(data.frame(day=1:7,value=c(1,2,3,20,5,6,7)), "day", "value", threshold = 2)
#> # A tibble: 7 × 5
#>    time value score outlier method   
#>   <dbl> <dbl> <dbl> <lgl>   <chr>    
#> 1     1     1   1   FALSE   local_mad
#> 2     2     2   0.5 FALSE   local_mad
#> 3     3     3   0   FALSE   local_mad
#> 4     4    20   7.5 TRUE    local_mad
#> 5     5     5   1   FALSE   local_mad
#> 6     6     6   0.5 FALSE   local_mad
#> 7     7     7   1   FALSE   local_mad
```
