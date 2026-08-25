# Derive simple phenological landmarks from a continuous trajectory

Derive simple phenological landmarks from a continuous trajectory

## Usage

``` r
pheno_phenology(x, time = NULL, value = NULL, levels = c(0.1, 0.5, 0.9))
```

## Arguments

- x:

  Numeric response, data frame, or `pheno_series`.

- time:

  Time vector or column name.

- value:

  Value-column name when needed.

- levels:

  Relative rising-phase thresholds.

## Value

A tibble of threshold times, peak time and optional senescence onset.

## Examples

``` r
d <- subset(pheno_data("growth_series"), plant_id == "P01" & trait == "leaf_area")
pheno_phenology(d, "day", "value")
#> # A tibble: 1 × 5
#>   peak_time peak_value rise_t10 rise_t50 rise_t90
#>       <dbl>      <dbl>    <dbl>    <dbl>    <dbl>
#> 1        28       117.        7       14       21
pheno_phenology(d, "day", "value", levels = c(.2,.5,.8))
#> # A tibble: 1 × 5
#>   peak_time peak_value rise_t20 rise_t50 rise_t80
#>       <dbl>      <dbl>    <dbl>    <dbl>    <dbl>
#> 1        28       117.        7       14       21
pheno_phenology(c(1,2,5,9,12,11,8), 0:6)
#> # A tibble: 1 × 5
#>   peak_time peak_value rise_t10 rise_t50 rise_t90
#>       <dbl>      <dbl>    <dbl>    <dbl>    <dbl>
#> 1         4         12        1        2        4
```
