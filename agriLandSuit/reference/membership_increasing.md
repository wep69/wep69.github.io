# Monotonically increasing fuzzy membership

Monotonically increasing fuzzy membership

## Usage

``` r
membership_increasing(x, unsuitable, suitable)
```

## Arguments

- x:

  Numeric values or a \`terra::SpatRaster\`.

- unsuitable:

  Values at or below this limit have membership zero.

- suitable:

  Values at or above this limit have membership one.

## Value

Object of the same data type as \`x\`, with membership in \[0, 1\].

## Examples

``` r
membership_increasing(c(50, 90, 120, 150, 200), unsuitable = 90, suitable = 150)
#> [1] 0.0 0.0 0.5 1.0 1.0
```
