# Multiscale texture extraction

Evaluates the same texture methods at several deterministic spatial
scales created by integer down-sampling. This exposes scale sensitivity
instead of silently mixing resolutions.

## Usage

``` r
pheno_texture_multiscale(
  x,
  scales = c(1L, 2L, 4L),
  methods = c("first_order", "glcm", "lbp", "fft"),
  mask = NULL,
  ...
)
```

## Arguments

- x:

  Image/gray matrix.

- scales:

  Integer down-sampling factors; 1 is the original resolution.

- methods:

  Texture methods passed to
  [`pheno_texture()`](https://wep69.github.io/OmniPhenoR/reference/pheno_texture.md).

- mask:

  Optional ROI mask, down-sampled with the same factors.

- ...:

  Passed to
  [`pheno_texture()`](https://wep69.github.io/OmniPhenoR/reference/pheno_texture.md).

## Value

A `pheno_texture_multiscale` list and long feature table.

## Examples

``` r
m1 <- pheno_texture_multiscale(
  pheno_data("leaf_gray"), scales = c(1, 2),
  methods = c("first_order", "glcm")
)
head(m1$features)
#> # A tibble: 6 × 3
#>   scale feature            value
#>   <int> <chr>              <dbl>
#> 1     1 first_order_min    0.322
#> 2     1 first_order_q05    0.422
#> 3     1 first_order_q25    0.670
#> 4     1 first_order_median 0.670
#> 5     1 first_order_q75    0.670
#> 6     1 first_order_q95    0.670
m2 <- pheno_texture_multiscale(pheno_data("leaf_gray"), scales=c(1,2,4), methods="fft")
m3 <- pheno_texture_multiscale(
  pheno_data("leaf_rgb"), scales = c(1, 2), methods = "lbp",
  mask = pheno_data("leaf_mask")
)
```
