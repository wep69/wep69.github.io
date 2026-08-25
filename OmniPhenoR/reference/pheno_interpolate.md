# Standalone temporal interpolation

Standalone temporal interpolation

## Usage

``` r
pheno_interpolate(
  time,
  value,
  grid,
  method = c("linear", "constant"),
  max_gap = Inf
)
```

## Arguments

- time, value:

  Observed time and values.

- grid:

  Target grid.

- method:

  `linear` or `constant`.

- max_gap:

  Maximum distance to the nearest observation.

## Value

Tibble.

## Examples

``` r
pheno_interpolate(c(0,2,5),c(1,3,7),0:5)
#> # A tibble: 6 × 3
#>    time value distance_to_observation
#>   <int> <dbl>                   <dbl>
#> 1     0  1                          0
#> 2     1  2                          1
#> 3     2  3                          0
#> 4     3  4.33                       1
#> 5     4  5.67                       1
#> 6     5  7                          0
pheno_interpolate(c(0,2,5),c(1,3,7),0:5,"constant")
#> # A tibble: 6 × 3
#>    time value distance_to_observation
#>   <int> <dbl>                   <dbl>
#> 1     0     1                       0
#> 2     1     1                       1
#> 3     2     3                       0
#> 4     3     3                       1
#> 5     4     3                       1
#> 6     5     7                       0
pheno_interpolate(c(0,5),c(1,7),0:5,max_gap=1)
#> # A tibble: 6 × 3
#>    time value distance_to_observation
#>   <int> <dbl>                   <dbl>
#> 1     0   1                         0
#> 2     1   2.2                       1
#> 3     2  NA                         2
#> 4     3  NA                         2
#> 5     4   5.8                       1
#> 6     5   7                         0
```
