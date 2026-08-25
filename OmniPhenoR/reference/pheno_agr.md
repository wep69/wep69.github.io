# Calculate interval absolute growth rate

Calculate interval absolute growth rate

## Usage

``` r
pheno_agr(x, time = NULL, value = NULL)
```

## Arguments

- x:

  Numeric response, data frame, or `pheno_series`.

- time:

  Time vector or column name.

- value:

  Value-column name when needed.

## Value

A tibble of interval growth rates.

## Examples

``` r
d <- subset(pheno_data("growth_series"), plant_id == "P01" & trait == "leaf_area")
pheno_agr(d, "day", "value")
#> # A tibble: 4 × 3
#>   time_start time_end   agr
#>        <dbl>    <dbl> <dbl>
#> 1          0        7  2.79
#> 2          7       14  4.67
#> 3         14       21  5.07
#> 4         21       28  2.06
pheno_agr(c(10, 14, 21), c(0, 2, 5))
#> # A tibble: 2 × 3
#>   time_start time_end   agr
#>        <dbl>    <dbl> <dbl>
#> 1          0        2  2   
#> 2          2        5  2.33
tail(pheno_agr(d, "day", "value"), 3)
#> # A tibble: 3 × 3
#>   time_start time_end   agr
#>        <dbl>    <dbl> <dbl>
#> 1          7       14  4.67
#> 2         14       21  5.07
#> 3         21       28  2.06
```
