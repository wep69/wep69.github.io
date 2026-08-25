# Gray-level co-occurrence matrix texture features

Computes Haralick-style second-order descriptors for user-declared pixel
distances and directions. Quantization, direction, distance, symmetry,
and region of interest are always returned with the result.

## Usage

``` r
pheno_glcm(
  x,
  levels = 32L,
  distances = 1L,
  angles = c(0, 45, 90, 135),
  symmetric = TRUE,
  mask = NULL
)
```

## Arguments

- x:

  Numeric gray matrix or RGB input.

- levels:

  Number of gray levels after quantization.

- distances:

  Positive integer pixel distances.

- angles:

  Angles in degrees. Version 0.1.0 supports 0, 45, 90, and 135.

- symmetric:

  If `TRUE`, count both directions for each pair.

- mask:

  Optional region-of-interest mask.

## Value

A tibble with one row per distance-angle combination.

## References

Haralick RM, Shanmugam K, Dinstein I (1973). Textural Features for Image
Classification. *IEEE Transactions on Systems, Man, and Cybernetics*,
SMC-3(6), 610-621.
[doi:10.1109/TSMC.1973.4309314](https://doi.org/10.1109/TSMC.1973.4309314)
.

## Examples

``` r
pheno_glcm(pheno_data("leaf_gray"), levels=16, angles=c(0,90))
#> # A tibble: 2 × 12
#>   distance angle contrast dissimilarity homogeneity   ASM energy entropy
#>      <int> <dbl>    <dbl>         <dbl>       <dbl> <dbl>  <dbl>   <dbl>
#> 1        1     0    0.887         0.231       0.921 0.582  0.763    1.34
#> 2        1    90    1.73          0.344       0.905 0.574  0.758    1.41
#> # ℹ 4 more variables: correlation <dbl>, max_probability <dbl>, mean_i <dbl>,
#> #   variance_i <dbl>
pheno_glcm(pheno_data("leaf_gray"), mask=pheno_data("leaf_mask"), distances=c(1,2), levels=32)
#> # A tibble: 8 × 12
#>   distance angle contrast dissimilarity homogeneity    ASM energy entropy
#>      <dbl> <dbl>    <dbl>         <dbl>       <dbl>  <dbl>  <dbl>   <dbl>
#> 1        1     0     5.36          1.45       0.526 0.0241  0.155    4.27
#> 2        1    45     9.77          1.91       0.474 0.0206  0.144    4.45
#> 3        1    90     8.96          1.82       0.482 0.0212  0.146    4.40
#> 4        1   135    10.0           1.95       0.472 0.0204  0.143    4.45
#> 5        2     0     9.07          1.92       0.458 0.0204  0.143    4.45
#> 6        2    45    18.2           2.84       0.367 0.0156  0.125    4.70
#> 7        2    90    15.7           2.57       0.392 0.0170  0.130    4.61
#> 8        2   135    17.8           2.86       0.358 0.0154  0.124    4.70
#> # ℹ 4 more variables: correlation <dbl>, max_probability <dbl>, mean_i <dbl>,
#> #   variance_i <dbl>
z <- matrix(rep(c(0,1),50),10); pheno_glcm(z, levels=2, angles=c(0,90))
#> # A tibble: 2 × 12
#>   distance angle contrast dissimilarity homogeneity   ASM energy entropy
#>      <int> <dbl>    <dbl>         <dbl>       <dbl> <dbl>  <dbl>   <dbl>
#> 1        1     0        0             0         1     0.5  0.707   0.693
#> 2        1    90        1             1         0.5   0.5  0.707   0.693
#> # ℹ 4 more variables: correlation <dbl>, max_probability <dbl>, mean_i <dbl>,
#> #   variance_i <dbl>
```
