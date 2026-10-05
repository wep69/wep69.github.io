# Apply monthly change factors to monthly series

Apply monthly change factors to monthly series

## Usage

``` r
apply_change_factors(x, factors, type = c("delta", "ratio"), month = NULL)
```

## Arguments

- x:

  Matrix of monthly values (units x months).

- factors:

  Matrix of factors (units x 12) or (units x months).

- type:

  \`delta\` (added) or \`ratio\` (multiplied).

- month:

  Integer month of each column of \`x\`, used when \`factors\` has 12
  columns.

## Value

Matrix like \`x\`.

## Examples

``` r
x <- matrix(rep(c(150, 120, 80, 20, 5, 0, 0, 0, 10, 40, 90, 140), 2), nrow = 1)
f <- matrix(seq(0.9, 1.1, length.out = 12), nrow = 1)
apply_change_factors(x, f, "ratio", month = rep(1:12, 2))
#>      [,1]     [,2]     [,3]     [,4]     [,5] [,6] [,7] [,8]     [,9]    [,10]
#> [1,]  135 110.1818 74.90909 19.09091 4.863636    0    0    0 10.45455 42.54545
#>         [,11] [,12] [,13]    [,14]    [,15]    [,16]    [,17] [,18] [,19] [,20]
#> [1,] 97.36364   154   135 110.1818 74.90909 19.09091 4.863636     0     0     0
#>         [,21]    [,22]    [,23] [,24]
#> [1,] 10.45455 42.54545 97.36364   154
apply_change_factors(x, matrix(1.5, 1, 12), "delta", month = rep(1:12, 2))
#>       [,1]  [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9] [,10] [,11] [,12] [,13]
#> [1,] 151.5 121.5 81.5 21.5  6.5  1.5  1.5  1.5 11.5  41.5  91.5 141.5 151.5
#>      [,14] [,15] [,16] [,17] [,18] [,19] [,20] [,21] [,22] [,23] [,24]
#> [1,] 121.5  81.5  21.5   6.5   1.5   1.5   1.5  11.5  41.5  91.5 141.5
```
