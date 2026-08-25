# Histogram of oriented gradients texture descriptor

Calculates Sobel gradients and a global orientation histogram weighted
by gradient magnitude. The global form is intentionally transparent; it
is suited to phenotypic texture summaries rather than object-detection
HOG block normalization.

## Usage

``` r
pheno_hog(x, bins = 9L, signed = FALSE, mask = NULL)
```

## Arguments

- x:

  Numeric gray matrix or RGB input.

- bins:

  Number of orientation bins.

- signed:

  If `FALSE`, use orientations in \[0,180); otherwise \[0,360).

- mask:

  Optional region-of-interest mask.

## Value

A list with summary and normalized histogram.

## References

Dalal N, Triggs B (2005). Histograms of Oriented Gradients for Human
Detection. *Proceedings of CVPR 2005*, 1, 886-893.
[doi:10.1109/CVPR.2005.177](https://doi.org/10.1109/CVPR.2005.177) .

## Examples

``` r
pheno_hog(pheno_data("leaf_gray"))$summary
#> # A tibble: 1 × 4
#>   total_gradient mean_gradient orientation_entropy dominant_orientation
#>            <dbl>         <dbl>               <dbl>                <dbl>
#> 1          1896.         0.154                1.53                   90
pheno_hog(pheno_data("leaf_gray"), bins=18, mask=pheno_data("leaf_mask"))$histogram
#> # A tibble: 18 × 4
#>      bin angle_mid weight probability
#>    <int>     <dbl>  <dbl>       <dbl>
#>  1     1         5   17.4      0.0329
#>  2     2        15   12.4      0.0235
#>  3     3        25   17.1      0.0323
#>  4     4        35   14.5      0.0274
#>  5     5        45   34.7      0.0658
#>  6     6        55   26.8      0.0508
#>  7     7        65   44.5      0.0842
#>  8     8        75   28.2      0.0534
#>  9     9        85   69.4      0.132 
#> 10    10        95   70.3      0.133 
#> 11    11       105   31.7      0.0601
#> 12    12       115   44.5      0.0843
#> 13    13       125   24.9      0.0471
#> 14    14       135   29.5      0.0559
#> 15    15       145   17.1      0.0324
#> 16    16       155   15.4      0.0291
#> 17    17       165   12.4      0.0235
#> 18    18       175   17.1      0.0325
z <- matrix(rep(1:20,each=20),20); pheno_hog(z,bins=9)$summary
#> # A tibble: 1 × 4
#>   total_gradient mean_gradient orientation_entropy dominant_orientation
#>            <dbl>         <dbl>               <dbl>                <dbl>
#> 1          5817.          14.5               0.900                   10
```
