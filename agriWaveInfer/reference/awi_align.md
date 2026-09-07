# Align scientific series

Aligns multiple scientific series by observed time coordinates.

## Usage

``` r
awi_align(..., join = c("inner", "full"), na_action = c("error", "omit"))
```

## Arguments

- ...:

  Two or more awi_series objects.

- join:

  Inner or full time join.

- na_action:

  Whether missing aligned values are errors or explicitly omitted.

## Value

An aligned data frame with class `awi_aligned`.
