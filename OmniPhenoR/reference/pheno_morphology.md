# Measure morphology of connected objects in a binary mask

Calculates object area, perimeter, bounding dimensions, centroid,
equivalent diameter, circularity, aspect ratio, and ellipse-based
eccentricity. Physical scale is propagated when a
[`pheno_calibrate_scale()`](https://wep69.github.io/OmniPhenoR/reference/pheno_calibrate_scale.md)
object is supplied.

## Usage

``` r
pheno_morphology(mask, scale = NULL, connectivity = c(8L, 4L), min_size = 1L)
```

## Arguments

- mask:

  Logical or binary matrix.

- scale:

  Optional `pheno_scale` object.

- connectivity:

  Four- or eight-neighbour component connectivity.

- min_size:

  Minimum object area in pixels.

## Value

A tibble with one row per connected object.

## Examples

``` r
pheno_morphology(pheno_data("leaf_mask"))
#> # A tibble: 1 × 13
#>   object_id area_px perimeter_px centroid_x centroid_y bbox_width_px
#>       <int>   <int>        <int>      <dbl>      <dbl>         <dbl>
#> 1         1    2872          264       64.5       48.5            92
#> # ℹ 7 more variables: bbox_height_px <dbl>, equivalent_diameter_px <dbl>,
#> #   circularity <dbl>, aspect_ratio <dbl>, eccentricity <dbl>,
#> #   major_axis_px <dbl>, minor_axis_px <dbl>
sc <- pheno_calibrate_scale(100, 50, "mm"); pheno_morphology(pheno_data("leaf_mask"), scale=sc)
#> # A tibble: 1 × 18
#>   object_id area_px perimeter_px centroid_x centroid_y bbox_width_px
#>       <int>   <int>        <int>      <dbl>      <dbl>         <dbl>
#> 1         1    2872          264       64.5       48.5            92
#> # ℹ 12 more variables: bbox_height_px <dbl>, equivalent_diameter_px <dbl>,
#> #   circularity <dbl>, aspect_ratio <dbl>, eccentricity <dbl>,
#> #   major_axis_px <dbl>, minor_axis_px <dbl>, area <dbl>, perimeter <dbl>,
#> #   equivalent_diameter <dbl>, major_axis <dbl>, minor_axis <dbl>
m <- matrix(FALSE,20,20); m[3:7,2:8] <- TRUE; m[12:18,12:17] <- TRUE; pheno_morphology(m)
#> # A tibble: 2 × 13
#>   object_id area_px perimeter_px centroid_x centroid_y bbox_width_px
#>       <int>   <int>        <int>      <dbl>      <dbl>         <dbl>
#> 1         1      35           24        5            5             7
#> 2         2      42           26       14.5         15             6
#> # ℹ 7 more variables: bbox_height_px <dbl>, equivalent_diameter_px <dbl>,
#> #   circularity <dbl>, aspect_ratio <dbl>, eccentricity <dbl>,
#> #   major_axis_px <dbl>, minor_axis_px <dbl>
```
