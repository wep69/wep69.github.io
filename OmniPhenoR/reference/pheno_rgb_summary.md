# Summarize RGB indices over a region of interest

Summarize RGB indices over a region of interest

## Usage

``` r
pheno_rgb_summary(
  x,
  indices = c("ExG", "NGRDI", "GLI", "VARI", "TGI"),
  mask = NULL
)
```

## Arguments

- x:

  RGB input.

- indices:

  Index names.

- mask:

  Optional logical matrix for array inputs.

## Value

A tibble with mean, SD, median, and selected quantiles per index.

## Examples

``` r
pheno_rgb_summary(pheno_data("leaf_rgb"),c("ExG","NGRDI","GLI"))
#> # A tibble: 3 × 7
#>   index     n    mean    sd median    q05   q95
#>   <chr> <int>   <dbl> <dbl>  <dbl>  <dbl> <dbl>
#> 1 ExG   12288  0.0265 0.442 -0.204 -0.204 0.951
#> 2 NGRDI 12288 -0.0349 0.287 -0.178 -0.178 0.572
#> 3 GLI   12288 -0.0115 0.285 -0.161 -0.161 0.576
pheno_rgb_summary(pheno_data("leaf_rgb"),c("VARI","TGI"),mask=pheno_data("leaf_mask"))
#> # A tibble: 2 × 7
#>   index     n   mean     sd median    q05    q95
#>   <chr> <int>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>
#> 1 VARI   2872  0.554  0.317  0.650 -0.313  0.799
#> 2 TGI    2872 34.6   11.5   39.1    2.85  39.6  
pheno_rgb_summary(data.frame(R=c(20,50,70),G=c(60,90,80),B=c(10,20,30)),c("ExG","RGBVI"))
#> # A tibble: 2 × 7
#>   index     n  mean    sd median   q05   q95
#>   <chr> <int> <dbl> <dbl>  <dbl> <dbl> <dbl>
#> 1 ExG       3 0.674 0.334  0.688 0.369 0.969
#> 2 RGBVI     3 0.727 0.200  0.780 0.533 0.883
```
