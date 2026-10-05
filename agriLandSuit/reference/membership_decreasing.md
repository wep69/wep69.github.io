# Monotonically decreasing fuzzy membership

Monotonically decreasing fuzzy membership

## Usage

``` r
membership_decreasing(x, suitable, unsuitable)
```

## Arguments

- x:

  Numeric values or a \`terra::SpatRaster\`.

- suitable:

  Values at or below this limit have membership one.

- unsuitable:

  Values at or above this limit have membership zero.

## Value

Object of the same data type as \`x\`, with membership in \[0, 1\].

## Examples

``` r
membership_decreasing(c(0, 5, 10, 20, 30), suitable = 5, unsuitable = 20)
#> [1] 1.0000000 1.0000000 0.6666667 0.0000000 0.0000000
```
