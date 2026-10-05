# Fill monthly gaps by the unit's own monthly climatology

Missing values are replaced by the mean of the same calendar month in
the same unit (station or cell). The positions that were filled are
returned in the \`filled\` attribute so they can be reported.

## Usage

``` r
fill_monthly_gaps(x, month)
```

## Arguments

- x:

  Numeric vector (one unit) or matrix with units in rows and months in
  columns.

- month:

  Integer month of each column (or element).

## Value

Object of the same shape as \`x\`, with attribute \`filled\` (logical,
same shape) and \`n_filled\` (per unit).

## Examples

``` r
x <- c(10, 20, NA, 40, 12, 22, 30, 42)
f <- fill_monthly_gaps(x, month = rep(1:4, 2))
f
#> [1] 10 20 30 40 12 22 30 42
#> attr(,"filled")
#> [1] FALSE FALSE  TRUE FALSE FALSE FALSE FALSE FALSE
#> attr(,"n_filled")
#> [1] 1
attr(f, "filled")
#> [1] FALSE FALSE  TRUE FALSE FALSE FALSE FALSE FALSE
```
