# Trapezoidal fuzzy membership

Trapezoidal fuzzy membership

## Usage

``` r
membership_trapezoid(x, absolute_min, optimum_min, optimum_max, absolute_max)
```

## Arguments

- x:

  Numeric values or a \`terra::SpatRaster\`.

- absolute_min:

  Lower support limit.

- optimum_min:

  Lower optimum limit.

- optimum_max:

  Upper optimum limit.

- absolute_max:

  Upper support limit.

## Value

Object of the same data type as \`x\`, with membership in \[0, 1\].

## Examples

``` r
membership_trapezoid(c(300, 500, 800, 1500, 2000), absolute_min = 400, optimum_min = 600,
                     optimum_max = 1200, absolute_max = 1800)
#> [1] 0.0 0.5 1.0 0.5 0.0
```
