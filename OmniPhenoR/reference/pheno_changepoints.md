# Detect a dominant temporal changepoint

Native methods search for the single split minimizing within-segment
SSE. Optional `method = "pelt"` delegates to the `changepoint` package.

## Usage

``` r
pheno_changepoints(
  x,
  time = NULL,
  value = NULL,
  method = c("mean", "slope", "pelt"),
  min_segment = 3L
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

  `"mean"`, `"slope"`, or optional `"pelt"`.

- min_segment:

  Minimum observations on each side of a native split.

## Value

A tibble describing detected changepoints.

## References

Killick R, Fearnhead P, Eckley IA (2012). JASA 107:1590-1598.
[doi:10.1080/01621459.2012.737745](https://doi.org/10.1080/01621459.2012.737745)
.

## Examples

``` r
y <- c(1,1.1,1.2,1.3,3,3.1,3.2,3.3); pheno_changepoints(y, 1:8)
#> # A tibble: 1 × 4
#>   index  time score method
#>   <int> <dbl> <dbl> <chr> 
#> 1     4     4   0.1 mean  
pheno_changepoints(c(1,2,3,4,8,12,16,20), 1:8, method = "slope")
#> # A tibble: 1 × 4
#>   index  time score method
#>   <int> <dbl> <dbl> <chr> 
#> 1     4     4     0 slope 
pheno_changepoints(data.frame(day=1:10, value=c(rep(2,5),rep(5,5))), "day", "value")
#> # A tibble: 1 × 4
#>   index  time score method
#>   <int> <dbl> <dbl> <chr> 
#> 1     5     5     0 mean  
```
