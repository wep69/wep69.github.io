# Triangular fuzzy membership

Triangular fuzzy membership

## Usage

``` r
membership_triangular(x, lower, optimum, upper)
```

## Arguments

- x:

  Numeric values or a \`terra::SpatRaster\`.

- lower:

  Lower support limit.

- optimum:

  Value of maximum membership.

- upper:

  Upper support limit.

## Value

Object of the same data type as \`x\`, with membership in \[0, 1\].
