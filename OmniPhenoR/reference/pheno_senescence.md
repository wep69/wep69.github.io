# Derive senescence timing and rate from a declining phenotype

Derive senescence timing and rate from a declining phenotype

## Usage

``` r
pheno_senescence(x, time = NULL, value = NULL, onset_fraction = 0.1)
```

## Arguments

- x:

  Numeric response, data frame, or `pheno_series`.

- time:

  Time vector or column name.

- value:

  Value-column name when needed.

- onset_fraction:

  Fractional decline from the post-peak range defining onset.

## Value

A one-row tibble with peak, onset, t50 and maximum decline rate.

## Examples

``` r
d <- subset(pheno_data("growth_series"), plant_id == "P01" & trait == "green_fraction")
pheno_senescence(d, "day", "value")
#> # A tibble: 1 × 5
#>   peak_time peak_value senescence_onset t50_senescence max_decline_rate
#>       <dbl>      <dbl>            <dbl>          <dbl>            <dbl>
#> 1         0      0.884               21             21          -0.0244
pheno_senescence(d, "day", "value", onset_fraction = .15)
#> # A tibble: 1 × 5
#>   peak_time peak_value senescence_onset t50_senescence max_decline_rate
#>       <dbl>      <dbl>            <dbl>          <dbl>            <dbl>
#> 1         0      0.884               21             21          -0.0244
pheno_senescence(c(1,.95,.8,.5,.2), 0:4)
#> # A tibble: 1 × 5
#>   peak_time peak_value senescence_onset t50_senescence max_decline_rate
#>       <dbl>      <dbl>            <dbl>          <dbl>            <dbl>
#> 1         0          1                1              3             -0.3
```
