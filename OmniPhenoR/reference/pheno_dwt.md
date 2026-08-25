# Two-dimensional Haar discrete-wavelet texture descriptors

Decomposes an image into approximation and horizontal, vertical, and
diagonal detail sub-bands. Energy and absolute-coefficient summaries are
returned for each level and orientation.

## Usage

``` r
pheno_dwt(x, levels = 2L)
```

## Arguments

- x:

  Numeric gray matrix or RGB input.

- levels:

  Number of decomposition levels.

## Value

A list containing a summary tibble and coefficient lists.

## References

Mallat SG (1989). A theory for multiresolution signal decomposition: the
wavelet representation. *IEEE Transactions on Pattern Analysis and
Machine Intelligence*, 11(7), 674-693.
[doi:10.1109/34.192463](https://doi.org/10.1109/34.192463) .

## Examples

``` r
pheno_dwt(pheno_data("leaf_gray"), levels=2)$summary
#> # A tibble: 6 × 6
#>   level band    energy mean_abs     sd max_abs
#>   <int> <chr>    <dbl>    <dbl>  <dbl>   <dbl>
#> 1     1 LH    0.000581  0.00624 0.0241   0.240
#> 2     1 HL    0.000287  0.00437 0.0170   0.262
#> 3     1 HH    0.000173  0.00354 0.0132   0.140
#> 4     2 LH    0.00329   0.0182  0.0574   0.371
#> 5     2 HL    0.00134   0.0124  0.0366   0.336
#> 6     2 HH    0.000647  0.00846 0.0255   0.185
pheno_dwt(pheno_data("leaf_gray"), levels=3)$summary
#> # A tibble: 9 × 6
#>   level band    energy mean_abs     sd max_abs
#>   <int> <chr>    <dbl>    <dbl>  <dbl>   <dbl>
#> 1     1 LH    0.000581  0.00624 0.0241   0.240
#> 2     1 HL    0.000287  0.00437 0.0170   0.262
#> 3     1 HH    0.000173  0.00354 0.0132   0.140
#> 4     2 LH    0.00329   0.0182  0.0574   0.371
#> 5     2 HL    0.00134   0.0124  0.0366   0.336
#> 6     2 HH    0.000647  0.00846 0.0255   0.185
#> 7     3 LH    0.0335    0.0693  0.184    0.871
#> 8     3 HL    0.0129    0.0417  0.114    0.690
#> 9     3 HH    0.00102   0.0139  0.0320   0.132
z <- outer(1:32,1:32,function(i,j) sin(i/3)+cos(j/4)); pheno_dwt(z,2)$summary
#> # A tibble: 6 × 6
#>   level band    energy mean_abs       sd  max_abs
#>   <int> <chr>    <dbl>    <dbl>    <dbl>    <dbl>
#> 1     1 LH    5.50e- 2 2.12e- 1 2.32e- 1 3.32e- 1
#> 2     1 HL    3.26e- 2 1.65e- 1 1.77e- 1 2.49e- 1
#> 3     1 HH    2.24e-33 2.37e-17 4.73e-17 2.21e-16
#> 4     2 LH    8.32e- 1 8.39e- 1 9.07e- 1 1.28e+ 0
#> 5     2 HL    5.09e- 1 6.70e- 1 7.01e- 1 9.80e- 1
#> 6     2 HH    3.03e-32 1.17e-16 1.75e-16 4.71e-16
```
