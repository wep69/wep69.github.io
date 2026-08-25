# Assess texture stability to orientation and scale

Assess texture stability to orientation and scale

## Usage

``` r
pheno_texture_stability(
  x,
  methods = c("first_order", "glcm", "lbp"),
  rotations = c(0, 90, 180, 270),
  scales = c(1L, 2L),
  ...
)
```

## Arguments

- x:

  Gray matrix or RGB image.

- methods:

  Texture methods.

- rotations:

  Rotations in multiples of 90 degrees.

- scales:

  Integer down-sampling factors.

- ...:

  Passed to
  [`pheno_texture()`](https://wep69.github.io/OmniPhenoR/reference/pheno_texture.md).

## Value

A list with transformed feature values and feature-level coefficient of
variation/range summaries.

## Examples

``` r
s1 <- pheno_texture_stability(
  pheno_data("leaf_gray"), methods = "first_order",
  rotations = c(0, 90), scales = 1
)
head(s1$summary)
#> # A tibble: 6 × 6
#>   feature                      mean    sd    cv range     n
#>   <chr>                       <dbl> <dbl> <dbl> <dbl> <int>
#> 1 first_order_cv              0.159     0     0     0     2
#> 2 first_order_entropy         1.26      0     0     0     2
#> 3 first_order_iqr             0         0    NA     0     2
#> 4 first_order_kurtosis_excess 0.833     0     0     0     2
#> 5 first_order_mad             0         0    NA     0     2
#> 6 first_order_max             0.670     0     0     0     2
s2 <- pheno_texture_stability(
  pheno_data("leaf_gray"), methods = "glcm", rotations = c(0, 90, 180),
  scales = c(1, 2), levels = 8
)
s3 <- pheno_texture_stability(
  outer(1:32, 1:32, function(i, j) sin(i / 3)), methods = "fft",
  rotations = c(0, 90), scales = c(1, 2)
)
```
