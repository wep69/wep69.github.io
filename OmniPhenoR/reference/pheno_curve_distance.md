# Distance between two aligned trajectories

Distance between two aligned trajectories

## Usage

``` r
pheno_curve_distance(
  x,
  y,
  method = c("euclidean", "correlation", "derivative")
)
```

## Arguments

- x, y:

  Numeric trajectories.

- method:

  `euclidean`, `correlation`, or `derivative`.

## Value

Numeric scalar.

## Examples

``` r
pheno_curve_distance(1:5,1:5)
#> [1] 0
pheno_curve_distance(1:5,5:1,"correlation")
#> [1] 2
pheno_curve_distance(c(1,2,4,7),c(1,3,5,7),"derivative")
#> [1] 1.414214
```
