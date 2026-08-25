# Gabor filter-bank texture descriptors

Applies real and imaginary Gabor kernels over declared wavelengths and
orientations, then summarizes response magnitude. This native
implementation is deterministic and designed for transparent feature
extraction. The optional `OpenImageR` backend is exposed through the
capability registry for future/high-performance extensions.

## Usage

``` r
pheno_gabor(
  x,
  wavelengths = c(4, 8),
  angles = c(0, 45, 90, 135),
  sigma = NULL,
  gamma = 0.5,
  size = NULL,
  mask = NULL
)
```

## Arguments

- x:

  Numeric gray matrix or RGB input.

- wavelengths:

  Positive wavelengths in pixels.

- angles:

  Orientations in degrees.

- sigma:

  Gaussian envelope standard deviation. By default `0.56*lambda`.

- gamma:

  Spatial aspect ratio.

- size:

  Odd kernel size. If `NULL`, it is chosen from sigma and capped.

- mask:

  Optional region-of-interest mask.

## Value

A tibble with one row per wavelength-angle pair.

## References

Gabor D (1946). Theory of communication. Part 1: The analysis of
information. *Journal of the Institution of Electrical Engineers - Part
III: Radio and Communication Engineering*, 93(26), 429-441.
[doi:10.1049/ji-3-2.1946.0074](https://doi.org/10.1049/ji-3-2.1946.0074)
.

## Examples

``` r
pheno_gabor(pheno_data("leaf_gray"), wavelengths=c(4,8), angles=c(0,90))
#> # A tibble: 4 × 7
#>   wavelength angle mean_response sd_response energy   q90 max_response
#>        <dbl> <dbl>         <dbl>       <dbl>  <dbl> <dbl>        <dbl>
#> 1          4     0         0.334       0.846  0.827 0.824         4.49
#> 2          4    90         0.221       0.734  0.588 0.259         4.49
#> 3          8     0         1.93        3.29  14.5   7.38         12.0 
#> 4          8    90         1.18        2.92   9.93  2.84         12.0 
pheno_gabor(
  pheno_data("leaf_gray"),
  wavelengths = 6,
  angles = c(0, 45, 90, 135),
  mask = pheno_data("leaf_mask")
)
#> # A tibble: 4 × 7
#>   wavelength angle mean_response sd_response energy   q90 max_response
#>        <dbl> <dbl>         <dbl>       <dbl>  <dbl> <dbl>        <dbl>
#> 1          6     0         0.783       0.457  0.822 1.48          2.37
#> 2          6    45         0.345       0.375  0.259 0.712         3.20
#> 3          6    90         0.229       0.230  0.105 0.473         1.79
#> 4          6   135         0.369       0.388  0.287 0.742         3.24
z <- outer(1:32,1:32,function(i,j) sin(j/2)); pheno_gabor(z,wavelengths=4,angles=c(0,90))
#> # A tibble: 2 × 7
#>   wavelength angle mean_response sd_response energy   q90 max_response
#>        <dbl> <dbl>         <dbl>       <dbl>  <dbl> <dbl>        <dbl>
#> 1          4     0         0.267       0.497  0.318 0.949         2.68
#> 2          4    90         1.49        1.03   3.28  3.35          4.89
```
