# Rank RGB indices against a binary mask or numeric target

For a binary target, ranking uses absolute standardized mean difference.
For a numeric target, ranking uses absolute correlation. The result is a
screening aid and does not replace independent validation.

## Usage

``` r
pheno_rgb_rank(
  x,
  target,
  indices = pheno_rgb_catalog()$index,
  method = c("pearson", "spearman")
)
```

## Arguments

- x:

  RGB input.

- target:

  Binary mask/vector or numeric target aligned with pixels/rows.

- indices:

  Indices to assess.

- method:

  Correlation method for numeric targets.

## Value

Ranked tibble.

## Examples

``` r
pheno_rgb_rank(pheno_data("leaf_rgb"), pheno_data("leaf_mask"), c("ExG","NGRDI","GLI","VARI"))
#> # A tibble: 4 × 4
#>   index metric                           score     n
#>   <chr> <chr>                            <dbl> <int>
#> 1 GLI   abs_standardized_mean_difference  7.07 12288
#> 2 ExG   abs_standardized_mean_difference  6.65 12288
#> 3 VARI  abs_standardized_mean_difference  6.18 12288
#> 4 NGRDI abs_standardized_mean_difference  4.94 12288
tab <- data.frame(R=c(.2,.3,.7,.8),G=c(.6,.5,.4,.3),B=c(.1,.2,.2,.2))
pheno_rgb_rank(tab, c(1,1,0,0), c("ExG","NGRDI","TGI"))
#> # A tibble: 3 × 4
#>   index metric                           score     n
#>   <chr> <chr>                            <dbl> <int>
#> 1 NGRDI abs_standardized_mean_difference  4.78     4
#> 2 TGI   abs_standardized_mean_difference  3.43     4
#> 3 ExG   abs_standardized_mean_difference  3.42     4
pheno_rgb_rank(tab, c(.9,.8,.3,.1), c("ExG","GLI"), method="spearman")
#> # A tibble: 2 × 4
#>   index metric                   score     n
#>   <chr> <chr>                    <dbl> <int>
#> 1 ExG   abs_spearman_correlation     1     4
#> 2 GLI   abs_spearman_correlation     1     4
```
