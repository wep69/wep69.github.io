# Validate RGB index numerical behaviour

Runs scale-representation checks for selected indices and reports finite
fractions and discrepancies between equivalent unit and byte RGB inputs.
This validates software behaviour, not biological validity of an index.

## Usage

``` r
pheno_rgb_validate(
  indices = c("ExG", "NGRDI", "GLI", "VARI", "TGI", "CIVE"),
  rgb = data.frame(R = c(0.2, 0.4, 0.6), G = c(0.6, 0.5, 0.7), B = c(0.1, 0.2, 0.3)),
  tolerance = 1e-10
)
```

## Arguments

- indices:

  RGB index names.

- rgb:

  Reference RGB triplets on unit scale.

- tolerance:

  Absolute unit-vs-byte tolerance.

## Value

A tibble with numerical validation diagnostics.

## Examples

``` r
pheno_rgb_validate(c("ExG","NGRDI","GLI"))
#> # A tibble: 3 × 7
#>   index scaling   finite_fraction unit_byte_max_abs_diff expected_representati…¹
#>   <chr> <chr>               <dbl>                  <dbl> <lgl>                  
#> 1 ExG   invariant               1               2.22e-16 TRUE                   
#> 2 NGRDI invariant               1               5.55e-17 TRUE                   
#> 3 GLI   invariant               1               0        TRUE                   
#> # ℹ abbreviated name: ¹​expected_representation_consistency
#> # ℹ 2 more variables: pass <lgl>, tolerance <dbl>
pheno_rgb_validate(c("TGI","CIVE"), tolerance = 1e-8)
#> # A tibble: 2 × 7
#>   index scaling finite_fraction unit_byte_max_abs_diff expected_representation…¹
#>   <chr> <chr>             <dbl>                  <dbl> <lgl>                    
#> 1 TGI   unit                  1                      0 TRUE                     
#> 2 CIVE  byte                  1                      0 TRUE                     
#> # ℹ abbreviated name: ¹​expected_representation_consistency
#> # ℹ 2 more variables: pass <lgl>, tolerance <dbl>
pheno_rgb_validate("VARI", rgb = data.frame(R=c(.2,.3),G=c(.6,.5),B=c(.1,.2)))
#> # A tibble: 1 × 7
#>   index scaling   finite_fraction unit_byte_max_abs_diff expected_representati…¹
#>   <chr> <chr>               <dbl>                  <dbl> <lgl>                  
#> 1 VARI  invariant               1               1.11e-16 TRUE                   
#> # ℹ abbreviated name: ¹​expected_representation_consistency
#> # ℹ 2 more variables: pass <lgl>, tolerance <dbl>
```
