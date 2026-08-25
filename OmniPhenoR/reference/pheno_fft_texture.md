# Frequency-domain texture descriptors

Summarizes the two-dimensional Fourier power spectrum with normalized
radial frequency, spectral entropy, centroid, dispersion, and
low/high-frequency power fractions. It complements spatially localized
wavelet descriptors.

## Usage

``` r
pheno_fft_texture(x, low_cut = 0.2, high_cut = 0.5)
```

## Arguments

- x:

  Numeric gray matrix or RGB input.

- low_cut:

  Normalized radial-frequency cutoff for low-frequency power.

- high_cut:

  Cutoff above which power is treated as high frequency.

## Value

A one-row tibble.

## Examples

``` r
pheno_fft_texture(pheno_data("leaf_gray"))
#> # A tibble: 1 × 7
#>   spectral_entropy spectral_centroid spectral_spread low_frequency_fraction
#>              <dbl>             <dbl>           <dbl>                  <dbl>
#> 1             3.51            0.0418          0.0881                  0.963
#> # ℹ 3 more variables: mid_frequency_fraction <dbl>,
#> #   high_frequency_fraction <dbl>, dominant_frequency <dbl>
pheno_fft_texture(pheno_data("leaf_gray"), low_cut=.15, high_cut=.45)
#> # A tibble: 1 × 7
#>   spectral_entropy spectral_centroid spectral_spread low_frequency_fraction
#>              <dbl>             <dbl>           <dbl>                  <dbl>
#> 1             3.51            0.0418          0.0881                  0.952
#> # ℹ 3 more variables: mid_frequency_fraction <dbl>,
#> #   high_frequency_fraction <dbl>, dominant_frequency <dbl>
z <- outer(1:64,1:64,function(i,j) sin(2*pi*j/8)); pheno_fft_texture(z)
#> # A tibble: 1 × 7
#>   spectral_entropy spectral_centroid spectral_spread low_frequency_fraction
#>              <dbl>             <dbl>           <dbl>                  <dbl>
#> 1            0.693             0.177        3.68e-16                      1
#> # ℹ 3 more variables: mid_frequency_fraction <dbl>,
#> #   high_frequency_fraction <dbl>, dominant_frequency <dbl>
```
