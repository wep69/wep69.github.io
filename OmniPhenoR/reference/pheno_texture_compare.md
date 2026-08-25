# Compare texture feature vectors between images

Compare texture feature vectors between images

## Usage

``` r
pheno_texture_compare(
  x,
  y,
  methods = c("first_order", "glcm", "lbp", "fft"),
  ...
)
```

## Arguments

- x:

  First image.

- y:

  Second image.

- methods:

  Texture methods.

- ...:

  Passed to
  [`pheno_texture()`](https://wep69.github.io/OmniPhenoR/reference/pheno_texture.md).

## Value

A list with paired feature table, correlation, RMSE, and cosine
similarity over features common to both images.

## Examples

``` r
x <- pheno_data("leaf_gray"); y <- x + matrix(stats::rnorm(length(x), 0, .01), nrow(x))
pheno_texture_compare(x,y,methods="first_order")$metrics
#> # A tibble: 1 × 4
#>       n correlation  rmse cosine_similarity
#>   <int>       <dbl> <dbl>             <dbl>
#> 1    17       0.891 0.378             0.917
pheno_texture_compare(x,t(x),methods="fft")$metrics
#> # A tibble: 1 × 4
#>       n correlation     rmse cosine_similarity
#>   <int>       <dbl>    <dbl>             <dbl>
#> 1     7           1 6.56e-19                 1
pheno_texture_compare(x,x,methods=c("first_order","glcm"),levels=8)$metrics
#> # A tibble: 1 × 4
#>       n correlation  rmse cosine_similarity
#>   <int>       <dbl> <dbl>             <dbl>
#> 1    57           1     0                 1
```
