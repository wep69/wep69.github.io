# Local binary pattern texture descriptor

Computes the classic 8-neighbour, radius-one local binary pattern (LBP)
and returns both a normalized histogram and compact summary statistics.
Optional rotation invariance uses the minimum circular bit rotation.

## Usage

``` r
pheno_lbp(x, rotation_invariant = FALSE, mask = NULL)
```

## Arguments

- x:

  Numeric gray matrix or RGB input.

- rotation_invariant:

  If `TRUE`, replace each 8-bit code by the minimum circular rotation of
  that code.

- mask:

  Optional region-of-interest mask.

## Value

A list with `summary`, `histogram`, and `codes`.

## References

Ojala T, Pietikainen M, Maenpaa T (2002). Multiresolution gray-scale and
rotation invariant texture classification with local binary patterns.
*IEEE Transactions on Pattern Analysis and Machine Intelligence*, 24(7),
971-987.
[doi:10.1109/TPAMI.2002.1017623](https://doi.org/10.1109/TPAMI.2002.1017623)
.

## Examples

``` r
pheno_lbp(pheno_data("leaf_gray"))$summary
#> # A tibble: 1 × 5
#>       n entropy uniformity uniform_pattern_probability dominant_code
#>   <int>   <dbl>      <dbl>                       <dbl>         <int>
#> 1 11844    1.57      0.615                       0.930           255
pheno_lbp(pheno_data("leaf_gray"), rotation_invariant=TRUE, mask=pheno_data("leaf_mask"))$summary
#> # A tibble: 1 × 5
#>       n entropy uniformity uniform_pattern_probability dominant_code
#>   <int>   <dbl>      <dbl>                       <dbl>         <int>
#> 1  2612    2.95     0.0826                       0.713           255
z <- outer(1:16,1:16,"+"); pheno_lbp(z)$histogram[1:8,]
#> # A tibble: 8 × 3
#>    code count probability
#>   <int> <int>       <dbl>
#> 1     0     0           0
#> 2     1     0           0
#> 3     2     0           0
#> 4     3     0           0
#> 5     4     0           0
#> 6     5     0           0
#> 7     6     0           0
#> 8     7     0           0
```
