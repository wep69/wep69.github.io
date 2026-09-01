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
