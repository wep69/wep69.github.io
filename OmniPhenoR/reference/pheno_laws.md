# Laws texture-energy descriptors

Applies the classic separable five-element Laws masks and summarizes
local response energy. Pairwise symmetric masks are averaged by name to
reduce orientation duplication while retaining edge, spot, wave, and
ripple content.

## Usage

``` r
pheno_laws(x, mask = NULL)
```

## Arguments

- x:

  Numeric gray matrix or RGB input.

- mask:

  Optional region-of-interest mask.

## Value

A tibble of filter-response energies.

## References

Laws KI (1980). Rapid texture identification. *Proceedings of SPIE*,
238, 376-381. Bibliographic page ranges vary across secondary databases;
see the package metadata-verification ledger.

## Examples

``` r
pheno_laws(pheno_data("leaf_gray"))
#> # A tibble: 10 × 4
#>    filter mean_abs  energy     sd
#>    <chr>     <dbl>   <dbl>  <dbl>
#>  1 L5E5     1.28   12.4    3.27  
#>  2 L5S5     0.593   2.38   1.42  
#>  3 L5W5     0.576   2.35   1.42  
#>  4 L5R5     1.14   11.4    3.19  
#>  5 E5S5     0.0530  0.0226 0.141 
#>  6 E5W5     0.0593  0.0262 0.151 
#>  7 E5R5     0.131   0.131  0.337 
#>  8 S5W5     0.0415  0.0111 0.0970
#>  9 S5R5     0.0941  0.0595 0.225 
#> 10 W5R5     0.117   0.0889 0.274 
pheno_laws(pheno_data("leaf_gray"), mask=pheno_data("leaf_mask"))
#> # A tibble: 10 × 4
#>    filter mean_abs energy    sd
#>    <chr>     <dbl>  <dbl> <dbl>
#>  1 L5E5      1.57  4.92   1.57 
#>  2 L5S5      0.628 0.723  0.574
#>  3 L5W5      0.585 0.646  0.552
#>  4 L5R5      1.16  2.68   1.15 
#>  5 E5S5      0.158 0.0542 0.172
#>  6 E5W5      0.179 0.0652 0.182
#>  7 E5R5      0.412 0.328  0.397
#>  8 S5W5      0.128 0.0293 0.114
#>  9 S5R5      0.300 0.161  0.267
#> 10 W5R5      0.373 0.246  0.327
z <- matrix(rep(c(0,1),512),32); pheno_laws(z)
#> # A tibble: 10 × 4
#>    filter mean_abs    energy    sd
#>    <chr>     <dbl>     <dbl> <dbl>
#>  1 L5E5     1.95     22.2    4.29 
#>  2 L5S5     0.977     6.12   2.27 
#>  3 L5W5     1.46     14.0    3.44 
#>  4 L5R5    62.0    3882.     6.07 
#>  5 E5S5     0.0156    0.0234 0.152
#>  6 E5W5     0.0234    0.0586 0.241
#>  7 E5R5     0.992     9.86   2.98 
#>  8 S5W5     0.0117    0.0117 0.108
#>  9 S5R5     0.496     1.99   1.32 
#> 10 W5R5     0.504     2.04   1.34 
```
