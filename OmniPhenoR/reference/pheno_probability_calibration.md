# Probability calibration diagnostics

Computes Brier score, log loss, expected calibration error (ECE), and a
bin-level calibration table for binary or multiclass classification.

## Usage

``` r
pheno_probability_calibration(
  truth,
  probability,
  classes = NULL,
  bins = 10L,
  eps = 1e-12
)
```

## Arguments

- truth:

  True class labels or integer class indices.

- probability:

  N x C probability matrix, or a vector of positive-class probabilities
  for binary outcomes.

- classes:

  Optional class labels matching probability columns.

- bins:

  Number of equal-width confidence bins.

- eps:

  Probability clipping tolerance.

## Value

A list with summary metrics and calibration table.

## References

Guo C, Pleiss G, Sun Y, Weinberger KQ (2017). On Calibration of Modern
Neural Networks. *Proceedings of Machine Learning Research*
70:1321-1330.

## Examples

``` r
pheno_probability_calibration(c(0,1,1,0), c(.1,.8,.7,.2), bins = 2)
#> $metrics
#> # A tibble: 1 × 4
#>   brier log_loss   ece accuracy
#>   <dbl>    <dbl> <dbl>    <dbl>
#> 1  0.09    0.227   0.2        1
#> 
#> $calibration
#> # A tibble: 2 × 6
#>     bin lower upper     n mean_confidence accuracy
#>   <int> <dbl> <dbl> <int>           <dbl>    <dbl>
#> 1     1   0     0.5     0            NA         NA
#> 2     2   0.5   1       4             0.8        1
#> 
#> $classes
#> [1] "0" "1"
#> 
pr <- rbind(c(.8,.2), c(.3,.7), c(.4,.6), c(.9,.1))
pheno_probability_calibration(c("A","B","B","A"), pr, classes = c("A","B"))
#> $metrics
#> # A tibble: 1 × 4
#>   brier log_loss   ece accuracy
#>   <dbl>    <dbl> <dbl>    <dbl>
#> 1  0.15    0.299  0.25        1
#> 
#> $calibration
#> # A tibble: 10 × 6
#>      bin lower upper     n mean_confidence accuracy
#>    <int> <dbl> <dbl> <int>           <dbl>    <dbl>
#>  1     1   0     0.1     0            NA         NA
#>  2     2   0.1   0.2     0            NA         NA
#>  3     3   0.2   0.3     0            NA         NA
#>  4     4   0.3   0.4     0            NA         NA
#>  5     5   0.4   0.5     0            NA         NA
#>  6     6   0.5   0.6     1             0.6        1
#>  7     7   0.6   0.7     1             0.7        1
#>  8     8   0.7   0.8     1             0.8        1
#>  9     9   0.8   0.9     1             0.9        1
#> 10    10   0.9   1       0            NA         NA
#> 
#> $classes
#> [1] "A" "B"
#> 
pheno_probability_calibration(c(1,2,2,1), pr, bins = 4)$metrics
#> # A tibble: 1 × 4
#>   brier log_loss   ece accuracy
#>   <dbl>    <dbl> <dbl>    <dbl>
#> 1  0.15    0.299  0.25        1
```
