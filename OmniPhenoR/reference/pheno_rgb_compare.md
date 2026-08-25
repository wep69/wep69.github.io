# Compare RGB index summaries across regions or groups

Compare RGB index summaries across regions or groups

## Usage

``` r
pheno_rgb_compare(x, indices = c("ExG", "NGRDI", "GLI", "VARI"), groups = NULL)
```

## Arguments

- x:

  RGB input.

- indices:

  Index names.

- groups:

  Optional vector aligned with pixels for tabular input, or matrix
  aligned with an image.

## Value

A tibble with group-wise mean, SD, median, and robust spread.

## Examples

``` r
img <- pheno_data("leaf_rgb"); g <- ifelse(pheno_data("leaf_mask"), "leaf", "background")
pheno_rgb_compare(img, c("ExG","GLI"), g)
#> # A tibble: 4 × 7
#>   index group          n   mean    sd median    mad
#>   <chr> <chr>      <int>  <dbl> <dbl>  <dbl>  <dbl>
#> 1 ExG   background  9416 -0.204 0     -0.204 0     
#> 2 ExG   leaf        2872  0.781 0.306  0.873 0.101 
#> 3 GLI   background  9416 -0.161 0     -0.161 0     
#> 4 GLI   leaf        2872  0.478 0.187  0.538 0.0512
tab <- data.frame(R=c(.2,.3,.7,.8),G=c(.6,.5,.4,.3),B=c(.1,.2,.2,.2))
pheno_rgb_compare(tab, c("NGRDI","VARI"), c("A","A","B","B"))
#> # A tibble: 4 × 7
#>   index group     n   mean    sd median   mad
#>   <chr> <chr> <int>  <dbl> <dbl>  <dbl> <dbl>
#> 1 NGRDI A         2  0.375 0.177  0.375 0.185
#> 2 NGRDI B         2 -0.364 0.129 -0.364 0.135
#> 3 VARI  A         2  0.452 0.168  0.452 0.176
#> 4 VARI  B         2 -0.444 0.157 -0.444 0.165
pheno_rgb_compare(img, "TGI")
#> # A tibble: 1 × 7
#>   index group     n  mean    sd median   mad
#>   <chr> <chr> <int> <dbl> <dbl>  <dbl> <dbl>
#> 1 TGI   all   12288 -8.09  24.2  -21.1     0
```
