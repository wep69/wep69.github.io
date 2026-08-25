# Apply simple channel-wise RGB reference calibration

Estimates multiplicative channel gains from an observed reference patch
to user-supplied target RGB values and applies those gains to the input
image. It is intentionally transparent and is not a substitute for a
complete radiometric calibration protocol.

## Usage

``` r
pheno_calibrate_rgb(x, observed, target, clip = NULL)
```

## Arguments

- x:

  RGB input accepted by
  [`pheno_rgb_indices()`](https://wep69.github.io/OmniPhenoR/reference/pheno_rgb_indices.md).

- observed:

  Length-three observed RGB reference values.

- target:

  Length-three target RGB values on the same scale.

- clip:

  Optional numeric length-two clipping limits.

## Value

Calibrated RGB input in the same broad representation as `x`.

## Examples

``` r
img <- pheno_data("leaf_rgb"); cal <- pheno_calibrate_rgb(img, c(.8,.78,.75), c(.9,.9,.9)); 
    dim(cal)
#> [1]  96 128   3
pheno_calibrate_rgb(data.frame(R=.4,G=.5,B=.3), c(.8,.8,.8), c(1,1,1))
#> # A tibble: 1 × 3
#>       R     G     B
#>   <dbl> <dbl> <dbl>
#> 1   0.5 0.625 0.375
pheno_calibrate_rgb(list(R=c(.2,.3),G=c(.5,.6),B=c(.1,.2)), c(.5,.5,.5), c(.6,.6,.6))
#> # A tibble: 2 × 3
#>       R     G     B
#>   <dbl> <dbl> <dbl>
#> 1  0.24  0.6   0.12
#> 2  0.36  0.72  0.24
```
