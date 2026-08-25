# Image and mask quality-control summary

Reports dimensions, finite-pixel fraction, dynamic range, channel
saturation, intensity moments, and a simple gradient-energy sharpness
proxy. These are screening diagnostics and should not be converted into
universal acceptance thresholds without calibration to the acquisition
protocol.

## Usage

``` r
pheno_qc(x, saturation_low = 0.01, saturation_high = 0.99)
```

## Arguments

- x:

  RGB image, gray matrix, or mask.

- saturation_low:

  Lower unit-scale saturation threshold.

- saturation_high:

  Upper unit-scale saturation threshold.

## Value

A one-row tibble.

## Examples

``` r
pheno_qc(pheno_data("leaf_rgb"))
#> # A tibble: 1 × 12
#>   height width finite_fraction missing_fraction    min   max dynamic_range  mean
#>    <int> <int>           <dbl>            <dbl>  <dbl> <dbl>         <dbl> <dbl>
#> 1     96   128               1                0 0.0769  0.86         0.783 0.649
#> # ℹ 4 more variables: sd <dbl>, low_saturation_fraction <dbl>,
#> #   high_saturation_fraction <dbl>, gradient_energy <dbl>
pheno_qc(pheno_data("leaf_gray"))
#> # A tibble: 1 × 12
#>   height width finite_fraction missing_fraction   min   max dynamic_range  mean
#>    <int> <int>           <dbl>            <dbl> <dbl> <dbl>         <dbl> <dbl>
#> 1     96   128               1                0 0.322 0.670         0.347 0.617
#> # ℹ 4 more variables: sd <dbl>, low_saturation_fraction <dbl>,
#> #   high_saturation_fraction <dbl>, gradient_energy <dbl>
pheno_qc(pheno_data("leaf_mask"))
#> # A tibble: 1 × 12
#>   height width finite_fraction missing_fraction   min   max dynamic_range  mean
#>    <int> <int>           <dbl>            <dbl> <dbl> <dbl>         <dbl> <dbl>
#> 1     96   128               1                0     0     1             1 0.234
#> # ℹ 4 more variables: sd <dbl>, low_saturation_fraction <dbl>,
#> #   high_saturation_fraction <dbl>, gradient_energy <dbl>
```
