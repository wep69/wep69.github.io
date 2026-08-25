# Map texture over an image grid

Map texture over an image grid

## Usage

``` r
pheno_texture_grid(
  x,
  window = 64L,
  step = window,
  methods = c("first_order", "glcm"),
  mask = NULL,
  min_fraction = 0,
  ...
)
```

## Arguments

- x:

  Gray matrix or RGB image.

- window:

  Window size in pixels.

- step:

  Grid step in pixels.

- methods:

  Texture methods.

- mask:

  Optional ROI mask; windows with less than `min_fraction` selected
  pixels are skipped.

- min_fraction:

  Minimum ROI fraction for a window.

- ...:

  Passed to
  [`pheno_texture()`](https://wep69.github.io/OmniPhenoR/reference/pheno_texture.md).

## Value

A tibble with window coordinates and flattened texture features.

## Examples

``` r
g1 <- pheno_texture_grid(pheno_data("leaf_gray"), window=32, step=32, methods="first_order")
head(g1)
#> # A tibble: 6 × 22
#>   row_start row_end col_start col_end roi_fraction first_order_min
#>       <int>   <int>     <int>   <int>        <dbl>           <dbl>
#> 1         1      32         1      32            1           0.670
#> 2         1      32        33      64            1           0.418
#> 3         1      32        65      96            1           0.412
#> 4         1      32        97     128            1           0.670
#> 5        33      64         1      32            1           0.397
#> 6        33      64        33      64            1           0.322
#> # ℹ 16 more variables: first_order_q05 <dbl>, first_order_q25 <dbl>,
#> #   first_order_median <dbl>, first_order_q75 <dbl>, first_order_q95 <dbl>,
#> #   first_order_max <dbl>, first_order_mean <dbl>, first_order_sd <dbl>,
#> #   first_order_variance <dbl>, first_order_cv <dbl>,
#> #   first_order_skewness <dbl>, first_order_kurtosis_excess <dbl>,
#> #   first_order_entropy <dbl>, first_order_uniformity <dbl>,
#> #   first_order_iqr <dbl>, first_order_mad <dbl>
g2 <- pheno_texture_grid(pheno_data("leaf_gray"), window=32, step=24, methods="glcm", levels=8)
g3 <- pheno_texture_grid(
  pheno_data("leaf_rgb"), window = 32, step = 32, methods = "lbp",
  mask = pheno_data("leaf_mask"), min_fraction = .2
)
```
