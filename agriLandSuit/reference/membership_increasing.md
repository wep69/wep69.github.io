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
