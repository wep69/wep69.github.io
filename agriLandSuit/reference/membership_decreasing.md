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
