# Quantify predictive uncertainty

Computes uncertainty from class probabilities. For segmentation arrays,
metrics are calculated pixel-wise. Entropy is normalized to 0–1 when
more than one class is present. Margin uncertainty is `1 - (p1 - p2)`
for the two largest class probabilities.

## Usage

``` r
pheno_uncertainty(
  probability,
  method = c("entropy", "margin", "confidence"),
  eps = 1e-12
)
```

## Arguments

- probability:

  Probability vector, matrix, or H x W x class array.

- method:

  `"entropy"`, `"margin"`, or `"confidence"`.

- eps:

  Numerical tolerance.

## Value

Numeric scalar or matrix, with summary attributes for image outputs.

## References

Gal Y, Ghahramani Z (2016). Dropout as a Bayesian Approximation:
Representing Model Uncertainty in Deep Learning. *Proceedings of Machine
Learning Research* 48:1050-1059.

## Examples

``` r
pheno_uncertainty(c(.8, .2), "entropy")
#> [1] 0.7219281
pheno_uncertainty(c(.55, .45), "margin")
#> [1] 0.9
p <- array(c(rep(.9, 16), rep(.1, 16)), c(4,4,2)); pheno_uncertainty(p, "confidence")
#>      [,1] [,2] [,3] [,4]
#> [1,]  0.1  0.1  0.1  0.1
#> [2,]  0.1  0.1  0.1  0.1
#> [3,]  0.1  0.1  0.1  0.1
#> [4,]  0.1  0.1  0.1  0.1
#> attr(,"summary")
#>   mean median    q95 
#>    0.1    0.1    0.1 
```
