# Gray-level run-length matrix texture features

Calculates directional run-length matrices and commonly used run-length
descriptors, including short- and long-run emphasis, gray-level and
run-length non-uniformity, run percentage, and low/high gray-level
emphasis.

## Usage

``` r
pheno_glrlm(x, levels = 32L, angles = c(0, 45, 90, 135), mask = NULL)
```

## Arguments

- x:

  Numeric gray matrix or RGB input.

- levels:

  Number of quantized gray levels.

- angles:

  Directions in degrees: 0, 45, 90, or 135.

- mask:

  Optional region-of-interest mask.

## Value

A tibble with one row per direction.

## References

Galloway MM (1975). Texture analysis using gray level run lengths.
*Computer Graphics and Image Processing*, 4(2), 172-179.
[doi:10.1016/S0146-664X(75)80008-6](https://doi.org/10.1016/S0146-664X%2875%2980008-6)
.

## Examples

``` r
pheno_glrlm(pheno_data("leaf_gray"), levels=16)
#> # A tibble: 4 × 9
#>   angle  runs   SRE   LRE   GLN   RLN    RP   LGRE  HGRE
#>   <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>  <dbl> <dbl>
#> 1     0  1696 0.632  590.  280.  634. 0.138 0.0343  66.2
#> 2    45  2027 0.585  162.  303.  652. 0.165 0.0421  81.6
#> 3    90  1924 0.611  279.  291.  681. 0.157 0.0413  72.3
#> 4   135  2051 0.594  160.  303.  680. 0.167 0.0425  81.3
pheno_glrlm(pheno_data("leaf_gray"), levels=16, angles=c(0,90), mask=pheno_data("leaf_mask"))
#> # A tibble: 2 × 9
#>   angle  runs   SRE   LRE   GLN   RLN    RP   LGRE  HGRE
#>   <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>  <dbl> <dbl>
#> 1     0  1560 0.688  7.57  293.  687. 0.543 0.0370  49.6
#> 2    90  1704 0.689  4.69  300.  766. 0.593 0.0461  48.6
z <- matrix(rep(c(0,0,1,1),25),10); pheno_glrlm(z, levels=2, angles=c(0,90))
#> # A tibble: 2 × 9
#>   angle  runs   SRE   LRE   GLN   RLN    RP  LGRE  HGRE
#>   <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
#> 1     0   100  1        1    50   100   1   0.625   2.5
#> 2    90    50  0.25     4    25    50   0.5 0.625   2.5
```
