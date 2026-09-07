# Scientific Data and Design Contracts

## Purpose

`agriWaveInfer` separates observations from analysis intent.
[`awi_series()`](https://wep69.github.io/agriWaveInfer/reference/awi_series.md)
and
[`awi_panel()`](https://wep69.github.io/agriWaveInfer/reference/awi_series.md)
carry observations; `AwiDesign` states what variable is the response,
which variables are predictors or controls, and which columns encode
time, grouping, space, crop identity, and units.

``` r

library(agriWaveInfer)

x <- awi_series(1:64, rnorm(64), variable = "soil moisture", unit = "z score")
awi_validate(x)
#> $valid
#> [1] TRUE
#> 
#> $problems
#> character(0)
#> 
#> $sampling_interval
#> [1] 1
#> 
#> attr(,"class")
#> [1] "awi_validation"

p <- data.frame(
  region = rep(c("A", "B"), each = 10),
  time = rep(1:10, 2),
  value = rnorm(20)
)
panel <- awi_panel(p, time = "time", value = "value", key = "region")
panel
#> <awi_panel>
#>  rows: 20 
#>  time: time 
#>  value: value 
#>  keys: region
```

## Missingness

Versions 0.1.0 and 0.2.0 do not interpolate or impute irregular/missing
time series. That decision is intentional: preprocessing changes
time-frequency structure and must be documented rather than hidden
inside a transform call.

## Alignment

``` r

a <- awi_series(1:10, rnorm(10), variable = "rain")
b <- awi_series(3:12, rnorm(10), variable = "ndvi")
awi_align(a, b, join = "inner")
#>   time        rain       ndvi
#> 1    3 -2.61233433  0.4861489
#> 2    4 -0.15569378  1.6728826
#> 3    5  0.43388979 -0.3543612
#> 4    6 -0.38195111  0.9463479
#> 5    7  0.42418757  1.3168264
#> 6    8  1.06310200 -0.2966400
#> 7    9  1.04871262 -0.3872136
#> 8   10 -0.03810289 -0.7854327
```

The current transform layer requires identical regular time coordinates.
More advanced missing-data and panel policies belong to later versions
after their scientific consequences are validated.
