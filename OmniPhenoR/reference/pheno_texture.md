# Unified texture-analysis interface

Runs complementary texture families through a single API. Version 0.1.0
includes first-order distribution summaries, GLCM/Haralick, GLRLM, LBP,
HOG, Gabor, Haar DWT, Laws energy, and FFT-domain descriptors.

## Usage

``` r
pheno_texture(
  x,
  methods = c("first_order", "glcm", "glrlm", "lbp", "hog", "gabor", "dwt", "laws",
    "fft"),
  mask = NULL,
  levels = 32L,
  distances = 1L,
  angles = c(0, 45, 90, 135),
  wavelengths = c(4, 8),
  dwt_levels = 2L,
  ...
)
```

## Arguments

- x:

  Numeric gray matrix or RGB input.

- methods:

  Character methods. Use `"all"` for all implemented families.

- mask:

  Optional region-of-interest mask.

- levels:

  Quantization levels for GLCM/GLRLM.

- distances:

  GLCM pixel distances.

- angles:

  Directions for GLCM, GLRLM, and Gabor.

- wavelengths:

  Gabor wavelengths.

- dwt_levels:

  Number of Haar DWT levels.

- ...:

  Reserved for compatible future controls.

## Value

A `pheno_texture` list with method-specific results and settings.

## Examples

``` r
t1 <- pheno_texture(pheno_data("leaf_gray"), c("first_order","glcm","lbp")); names(t1$results)
#> [1] "first_order" "glcm"        "lbp"        
t2 <- pheno_texture(
  pheno_data("leaf_gray"),
  c("glrlm", "gabor", "dwt"),
  mask = pheno_data("leaf_mask"),
  levels = 16
)
t2
#> <pheno_texture>
#>   methods: glrlm, gabor, dwt 
z <- outer(1:32, 1:32, function(i, j) sin(i / 2) + cos(j / 3))
pheno_texture(z, c("hog", "laws", "fft"))$results$fft
#> # A tibble: 1 × 7
#>   spectral_entropy spectral_centroid spectral_spread low_frequency_fraction
#>              <dbl>             <dbl>           <dbl>                  <dbl>
#> 1             2.43             0.104          0.0569                  0.970
#> # ℹ 3 more variables: mid_frequency_fraction <dbl>,
#> #   high_frequency_fraction <dbl>, dominant_frequency <dbl>
```
