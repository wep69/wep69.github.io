# Calculate interval relative growth rate

Calculate interval relative growth rate

## Usage

``` r
pheno_rgr(x, time = NULL, value = NULL, offset = .pheno_eps)
```

## Arguments

- x:

  Numeric response, data frame, or `pheno_series`.

- time:

  Time vector or column name.

- value:

  Value-column name when needed.

- offset:

  Positive offset used when values are zero/non-positive.

## Value

A tibble of interval relative growth rates.

## Examples

``` r
d <- subset(pheno_data("growth_series"), plant_id == "P01" & trait == "leaf_area")
pheno_rgr(d, "day", "value")
#> # A tibble: 4 × 3
#>   time_start time_end    rgr
#>        <dbl>    <dbl>  <dbl>
#> 1          0        7 0.122 
#> 2          7       14 0.0962
#> 3         14       21 0.0609
#> 4         21       28 0.0189
pheno_rgr(c(10, 14, 21), c(0, 2, 5))
#> # A tibble: 2 × 3
#>   time_start time_end   rgr
#>        <dbl>    <dbl> <dbl>
#> 1          0        2 0.168
#> 2          2        5 0.135
pheno_rgr(data.frame(day=0:3, value=c(.1,.2,.4,.8)), "day", "value", offset = 1e-6)
#> # A tibble: 3 × 3
#>   time_start time_end   rgr
#>        <dbl>    <dbl> <dbl>
#> 1          0        1 0.693
#> 2          1        2 0.693
#> 3          2        3 0.693
```
