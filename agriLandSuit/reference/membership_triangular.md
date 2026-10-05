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

## Examples

``` r
membership_triangular(c(-1, 0, 1, 2, 3, 4, 5), lower = 0, optimum = 2, upper = 4)
#> [1] 0.0 0.0 0.5 1.0 0.5 0.0 0.0
```
